# Building Microservices
## Introduction
The intent of this guide is to create a single source of information for building and scaling microservices systems. It will contain both theoretical information and more detailed practicle examples and technologies that can be used for solving different problems that can arise when dealing with distributed systems. 
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
## What are microservices?

> Collection of loosely coupled services modeled around a business domain.

Microservices architecture enables continuous delivery of large complex systems. It also makes for systems that can be scaled horizontally. It enables developers to evolve the technological stack that is used and it also allows for using the correct tool for each task. All of the benefits also make it pretty complex to implement properly.

## Principles of microservices

 ### Independently deployable
 Every microservice should be self contained and redeploying it should not    depend on any other microservice. 
 One service per host, one database per service. 
 
 - Docker
 - Contract Testing - Consumer of a service creates tests that indicate, which parts of the service's API they are using. Those tests are then executed as part of the continuous integration of the service, so that it can make sure the contract is not broken. This allows for api changes to be made with certainty that nothing is broken, also if something is broken we know exactly which consumer was broken.
 - How to break API contracts - Consumers can't and should not be forced to upgrade to the new API instantly, so provide the old version of the api as well as the new version until all consumers have migrated to the new one. This can be done in two ways:
	 - Simply expose both version of the API through the service
	 - Deploy two versions of the service itself, this is useful when there are consumers that can't be switched to the new version. This approach should be avoided as it complicates service discovery and if you have to fix a critical bug in the service you would need to fix it in two places.

 ### Modeled around a business domain
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
 
 - Correlation IDs
 - 
 - Aggregation of all logs

 ### Consumer first
 Make information about the service easily available to consumers.
 - Swagger
 - Wikis with additional information or even stats.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjQ3MTA3MDAsMTk0MjkxMzUzMSwtMj
A0NjQ3NzcxMiwtMTgxNjQwODI4NiwxNTE1NDU0OTQzLDQ2MzY3
ODQwOCwtMjA4Mjk0MzM5OCwxNzkwNjk2NDIxLC0xNjU2MTAzNj
UwLDExNDEwMDU4MDYsNTEwMjUxNDY4LDE5MTQ4MDE0MTgsMTM0
ODgwMDIyOSwtMTUwODU5OTMzMiw0MjIxMDI3NjcsODYzNTIwNT
E4LC02MzY2MTkxODMsODQzNTI0MzQ3LDQ1MzI4MTMzMiwtMTU2
NzYyOTA3NV19
-->