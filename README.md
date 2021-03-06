# Building Microservices
## Introduction
The intent of this article is to create a single source of information for building and scaling microservices systems. It will contain both theoretical information and more detailed practicle examples and technologies that can be used for solving different problems that can arise when dealing with distributed systems. 
A lot of the information is aggregated from different sources, they can be found at the end of the article.

### Contents
 - [What are microservices?](#what-are-microservices)
 - [Principles of microservices](#principles-of-microservices)
	 - [Independently deployable](#independently-deployable)
	 - [Modeled around a business domain](#modeled-around-a-business-domain)
	 - [Automate every aspect](#automate-every-aspect)
	 - [Hide implementation details](#hide-implementation-details)
	 - [Isolate failure](#isolate-failure)
	 - [Decentralize everything](#decentralize-everything)
	 - [Monitor everything](#monitor-everything)
	 - [Consumer first](#consumer-first)
 - [Data consistency across services](#data-consistency-across-services)
 - [Resources](#resources)

## What are microservices?

> Collection of loosely coupled services modeled around a business domain.

Microservices architecture enables continuous delivery of large complex systems. It also makes for systems that can be scaled horizontally. It enables developers to evolve the technological stack that is used and it also allows for using the correct tool for each task. All of these benefits also make it very complex to implement properly.

## Principles of microservices
The 8 principles of microservices as defined by Sam Newman.

 ### Independently deployable
 This is the most important principle of all.
 Every microservice should be self contained and redeploying it should not    depend on any other microservice. 
 One service per host, one database per service. 
 
 - Docker
 - Contract Testing - Consumer of a service creates tests that indicate, which parts of the service's API they are using. Those tests are then executed as part of the continuous integration of the service, so that it can make sure the contract is not broken. This allows for api changes to be made with certainty that nothing is broken, also if something is broken we know exactly which consumer was broken.
 - How to break API contracts - Consumers can't and should not be forced to upgrade to the new API instantly, so provide the old version of the api as well as the new version until all consumers have migrated to the new one. This can be done in two ways:
	 - Simply expose both version of the API through the service
	 - Deploy two versions of the service itself, this is useful when there are consumers that can't be switched to the new version. This approach should be avoided as it complicates service discovery and if you have to fix a critical bug in the service you would need to fix it in two places.

 ### Modeled around a business domain
 Every service should be modeled around a business domain rather than some kind of a technological grouping. This leads to every service having a more strictly defined boundaries and less changing APIs. This also means that the teams that build a microservice also become very knowledgeable in that part of the business domain. It also reduced the cross-cutting changes needed when implementing new features.
 
 ### Automate every aspect
 This principle requires a lot of time and effort upfront without a lot of benefit at first, but once everything is in place creating, maintaining and deploying a new service becomes trivial.
 
 - Infrastructure Automation
 - Automated Testing
 - Continuous Delivery

 ### Hide implementation details
 The only communication in a microservices system should happen through api calls between the services.

 - Focus on creating finely grained service APIs. Only expose what **needs** to be exposed.
 - Hide the databases. Every microservice should have its own database. This allows for deploying services independently. If two services read from the same schema updating the schema due to changes needed in service A would mean that service B would be impacted as well.
 
 ### Isolate failure
 With microservices comes network communication, which makes the whole system a lot easier to break than a monolith. The reliable in-process calls are changed with network calls to an outside service, which you can't guarantee will be there. 
 Every service should be fault tolerant and should be able to run (even at   diminished functionality) whenever another service that it connects to is down.
 When one service call fails it must fail quickly, so that it doesn't tie up resources and consumers of that service might programmatically degrade functionality.
 
 - Bulkhead Pattern - if a service doesn't respond in a timely manner it should not impact the ability of the consumer to call other services.
 - Timeouts - make more aggressive timeouts
 - Circuit Breakers
 - [Hystrix](https://github.com/Netflix/hystrix) - library designed to isolate points of access to remote systems. Helps with Circuit Breakers

 ### Decentralize everything
 The ideology here is to use dumb pipes and infrastructure and contain the logic only in the microservices. Message brokers shouldn't be aware of the domain, they need to be kept as simple as possible. 
 ### Monitor everything
 Centralize all information needed for monitoring the system. Logging on every node and checking for errors does not scale and cannot be automated.
 
 - Aggregation of stats
 - Aggregation of all logs
 - Correlation IDs - At the beginning of a call train generate an ID, which gets passed down the call train and gets recorded by every different service. If an error happens the ID is also recorded. This way via log aggregation you can check the whole life cycle of this request and it makes it a lot easier to debug issues once the system gets big and complicated. **The problem is that they become useful when the system gets big and complex, but once this happens correlation IDs become hard to implement, so they should be done right from the start.**

 ### Consumer first
 Make information about the service easily available to consumers.
 - Swagger
 - Wikis with additional information or even stats.

## Data consistency across services
### Problem
One of the big data consistency problems in microservice systems comes from the fact that each microservice has its own data store, which means that given the following example system:

![Example](/img/acid-example-services.PNG?raw=true)

In this rather simple case we want the Customer and Order services to only talk to each other via their APIs, not through their databases. This means that we cannot have a classic ACID transaction that writes in both the Customer DB and the Order DB, so we have to somehow coordinate the two services to write something in their respective databases AND if one of them fails the other one has to rollback as well.
This issue is not only present with databases. We might need transactions, which span across Messaging Queues AND Databases. Message brokers adding events to a queue is also a way of changing a data source.

### Possible solutions
- The best solution is to design your microservices in such a way that you don't need **synchronous** distributed transactions across multiple services. After all microservices architecture aims to avoid any kind of dependency between services, so having to coordinate two or more services to commit or rollback data at the same time would couple them quite a bit. In some cases we can try to avoid the need for cross service transactions, by making sure such business cases are encompased by a single microservice, so we need to have very well defined domain boundaries.
 - If we have to do distributed transactions the best way is to have one of the services be the "coordinator" of an asynchronous transaction. **This means that every business event would result in a single synchronous transaction and multiple asynchronous calls to remote services after it.** One service would usually commit a normal synchronous transaction to its database, persist events for calling remote services, return a response and then start asynchronously calling other services to complete the whole transaction and **eventually** achieve consistency. If an asynchronous transaction fails it needs to be retried until successful. The pattern that should be used to achieve this is: **Commander, Retries and Idempotence**
	 - **Commander** - The service that coordinates the asynchronous transaction. It knows the instructions that need to be executed. It has to execute the remote calls to other services and once they are successful it needs to remove the events from its persistant event queue.
	 - **Retries** - If an asynchronos event fails for whatever reason, the commander needs to retry it until it's completed. If the commander receives a successful response, but fails to remove the event from it's queue it needs to retry that too. If some other failure scenario happens the answer is retry.
	 - **Idempotence** - This is the property of doing something twice and having the same result as if it was done only once. We need idempotence at the remote service or data source so that, in the cases where it receives the instruction more than once, it only processes it once.
 - Sagas - TODO
 - Two-phase commit (2PC) protocol - This approach can work in some cases, but is generally avoided as it has a few problems:
	 - The 2PC coordinator is a single point of failure, which we want to avoid in microservices.
	 - It's quite slow in a distributed system.
	 - Reduced throughput due to locks
	 - Not supported by many NoSQL databases
	 - Impacts availability

## Resources
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3Nzc0ODgwNywtNjIwNzk0NTI5LC0xNz
c3NDg4MDcsMTA1NzI3NDcyMCwtMTUyMjUwOTk2NywxNjI5NjU5
MTcxLC00NTk1MDMxNjEsMTEzNTcxMzM5OCwtMTc5MTIxNzk3Ni
wtMTM5NjM1NzkwMywtMTM3NzE5Mzc5OSw5NjY1NDc0MjIsMTA1
MDg2Mzg2OCwtNzA2NjE3MDksLTE1OTg1NzY2MzgsNzM4MDE3Mj
g4LDY3NzI2NDc4MCwtMjAxMTY4MzI5MiwtNzY4NzQ2MjQsNzcy
NDYzNjM0XX0=
-->