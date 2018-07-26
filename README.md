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
 - Hide the databases. Every microservice should have its own database. This allows for deploying services independently. If two services read from the same schema updating the schema due to changes needed in service A would mean that service B would be impacted as well.
 ### Isolate failure
 ### Decentralize everything
 ### Monitor everything
 ### Consumer first

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYzNjYxOTE4Myw4NDM1MjQzNDcsNDUzMj
gxMzMyLC0xNTY3NjI5MDc1LC00OTU1NDE2NTgsLTE0NTEwNTI1
MzgsMTU4MDkyOTA3NywxMjg5Njk5MzQ4LC0xMTQ2NjQwNzk4LC
0zODAxNTA2MzUsMjA5NDE1NTY2MiwtNjM4OTMwNDg1LDcyNjIz
MjIyOCw5NDI2MDEzOTEsMTU4OTI1MDU0NiwyMDMxOTI3MjA0XX
0=
-->