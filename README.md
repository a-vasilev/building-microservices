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

## Principles of microservices

 ### Independently deployable
 Every microservice should be self contained and redeploying it should not    depend on any other microservice. 
 ### Modeled around a business domain
 ### Automate every aspect
 This principle requires a lot of time and effort upfront without a lot of benefit at first, but once everything is in place creating, maintaining and deploying a new service becomes trivial.
 
 - Infrastructure Automation
 - Automated Testing
 - Continuous Delivery

 ### Hide implementation details
 The only communication in a microservices system should happen through api calls between the services.

 - Focus on creating finely grained service APIs. Only expose what **needs** to be exposed.
 - Hide the databases. Every microservice should have its own database. This allows for independen
 ### Isolate failure
 ### Decentralize everything
 ### Monitor everything
 ### Consumer first

<!--stackedit_data:
eyJoaXN0b3J5IjpbMjQ1MjY0MTY3LDg0MzUyNDM0Nyw0NTMyOD
EzMzIsLTE1Njc2MjkwNzUsLTQ5NTU0MTY1OCwtMTQ1MTA1MjUz
OCwxNTgwOTI5MDc3LDEyODk2OTkzNDgsLTExNDY2NDA3OTgsLT
M4MDE1MDYzNSwyMDk0MTU1NjYyLC02Mzg5MzA0ODUsNzI2MjMy
MjI4LDk0MjYwMTM5MSwxNTg5MjUwNTQ2LDIwMzE5MjcyMDRdfQ
==
-->