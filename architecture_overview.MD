# Architecture Overview

Providing and supporting high demand operations as needed by our solutions requires having a robust technological base with well known design patterns and best practices.  
The system is based on micro-services, this is a top-level pattern that is made up of dozens of sub-patterns to help break down the system into multiple, independent and reactive services with a very well defined responsibilitie.  These services are indepent from the design, development, deployment and maintenance perspective.  
To make feasible all the benefits offered by the micro-services standard, it is imperative to divide the system into independent µServices, with high cohesion and low coupling.  

## Domain Driven Design

Domain-Driven-Design, known as DDD, is a set of practices and guidelines for designing modular system, where each of the modules is independent and reactive.  
The first step is to completely forget the data-oriented designs since the worst mistake that can be make is to have a centralized data repository where the entire system is guided by the same entity-relationship design 'cause even if we divided the system In hundreds of backends, a change in the central data schema means a change in multiple backends and this means we lost µService independence.

The solution is implemented with Aggregates (ex. Device, User, Source) and the events that these aggregates can produce (ex. DeviceConnected, UserEnabled, SourceBlocked). At this point we favor basing the system on domain events and not on centralized data schemas. Having understood this, we can say that a µService in the ecosystem generates domain events and reacts to domain events produced by other µServices.  This is the only point of interaction between µServices. Using domain-events each µService is free to create its own database (called materialized view) with the required scheme and technology.  

If you want to learn more about DDD you could do so at:
- [DDD Book](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)
- [DDD Reference](https://domainlanguage.com/wp-content/uploads/2016/05/DDD_Reference_2015-03.pdf)

## CQRS & Event-Sourcing
Command-Query-Responsibility-Segregation dictates a µService must be broken into two parts, one for writing and one for reading.  

Event-Sourcing dictates that the state of an aggregate is obtained by reducing all the events of said aggregate. To explain it a little better, Event Sourcing dicatates that everything that happens with an aggregate (Ex. Account) should be stored as a sequence of events (Enabled, Credited, Debited, Blocked, etc.) persisted in an event repository over time called event-store. And, in the case of the Account aggregate, to retrieve the status of a specific Account, you should query all the existing events of that Account and calculate the current status of the Account with them.  

The NebulaE platform implements CQRS, where the writing part is carried out by the event-store and the reading part is carried out on a specific Database of the µService that is implemented in the technology and scheme that best suits the µService. The µService database is called the materialized view.  

![µService Interaction using domain-events](https://github.com/nebulae-interview/guideline/raw/master/res/service_interaction.png "µService Interaction using domain-events")

By implementing these techniques, multiple µServices can coexist on the Nebula platform without having to knwo other µServices. Each µService, as an independent processing unit, only react to events and generate other events.  

## FullStack MicroServices

NebulaE's platforms are entirelly based µServices and each of these µServices are fullstack µServices:
- Presentation: FrontEnd
- Service: API-Gateway
- Business/Domain: Backend
- Persistance/MaterializedView: MongoDB most of the time
- Deployment: GKE deployment specs (not included in the interview project)

NebulaE's platforms are composed by dozens of fullstack µServices.  In order to avoid publishing dozens of web portals and api end-points the framework implements ui-composition and api-composition design patterns:
- A FrontEnd SHELL is taken in place ready to include and publish all the micro-frontends from every µService.  
- A API SHELL is taken in place ready to include and publish all the micro-api from every µService.  


![µService Shell composition](https://github.com/nebulae-interview/guideline/raw/master/res/shell_composition.png "µService shell composition")

In this way, the micro-service continues to be totally independent, as it contains all the required layers.  And when the µService is being deployed then the micro-FrontEnd is hosted at the FrontEnd-Shell and the micro-API is hosted in the API-Shell.

Below is a high-level component diagram, which exposes the different modules and technologies applied on the NebulaE platform.

![µService Platform](https://github.com/nebulae-interview/guideline/raw/master/res/microservices_platform.png "µService Platform")



