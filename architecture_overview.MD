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

## MicroService data-flow

The following diagrams describes how all the layers of the µService interacts to achieve CQRS/ES reading and writing flows.  

Diagrams leyend:
- Solid line and white background numeral: Request flow - from the user to the processor
- Dashed line and black background numeral: Response flow -  from the procesor to the user
- Black line: synchronous communications (blocking)
- Blue Line: asynchronous communications (non-blocking using publish-subscribe)
- Red Line: communications triggered by domain-events published in the event-store

### READ: Querying data on the materialized view

![CQRS/ES Read flow](https://github.com/nebulae-interview/guideline/raw/master/res/read_workflow.png "CQRS/ES Read flow")

1. User makes a request to read information from the UI. 
2. The front-end makes a query using a synchronous HTTP call under the GraphQL API language.  
3. The API publishes the query request in the topic dedicated to the aggregate to which the query belongs to (eg Account). 
4. One of the µService's backend instances in charge of solving the request, obtaining the request from the aggregat-topic.  
5. The Backend queries the information in the materialized view. 
6. The materialized view returns the information of the queried aggregate. 
7. The backend publishes the result of the query in the response-topic. 
8. The API takes the answer from the respone-topic. 
9. The API returns the query response in the HTTP-RESPONSE of the original request. 
10. The front-end displays the information of the query to the user. 


### WRITE (Pure CQRS/ES)
Pure CQRS/ES is only intended for complex and highly-concurrent micro-services.  On DDD this would be the core microservices.

Writing data on the event-store and Materialized view:
![Pure CQRS/ES Write flow](https://github.com/nebulae-interview/guideline/raw/master/res/write_workflow_pure_cqrs_es.png "Pure CQRS/ES Write flow")

1. User performs a modification command from the UI. 
2. The front-end executes the command (mutation) by means of a synchronous HTTP call under the GraphQL language to the API.  
3. The API publishes the command in the topic dedicated to the aggregate to which the command belongs (eg Account). 
4. One of the µService's backend instances in charge of solving the request, obtaining the request from the aggregat-topic.  

From here two simultaneous flows are opened, one to indicate to the user that the command has been executed successfully and the other to reduce the generated domain-event and update the materialized-view.  

5a.The backend validates the conditions of the command to indicate if it is possible or not to make the change. If possible then responds with an ack meaning the command executed successfully. This answer is published in the response-topic.  
6a. The api takes the answer from the response-topic.  
7a. The api returns the command response in the HTTP-RESPONSE of the original request.  
8a. The front-end displays the command success confirmation.  


5b. The backend validates the conditions of the command to indicate if it is possible or not to make the change. If possible then the backend publishes a domain-event indicating the action taken on the aggregate (eg AccountEnabled).  
6b. The domain-event is shared to all µServices interested in that event.  
7b. The backend of each µService updates the materialized view as indicated by the event (Eg enabling the acount). The data is updated in the differents µServices ready to be query.  

### WRITE (CRUD Strategy + ES)
CRUD Strategy + ES is intended for data-centric µServices, the idea is to combine a CRUD strategy instead of CQRS and generate domian-event every time an Aggregate changes in order keep updated other µServices

<span style="color:red">**NOTE: This is the strategy used on the interview project**</span>

Writing data on the materialized-view and publishing the event-domain:

![CRUD/ES Write flow](https://github.com/nebulae-interview/guideline/raw/master/res/write_workflow_crud_es.png "CRUD/ES Write flow")

1. User performs a modification command from the UI. 
2. The front-end executes the command (mutation) by means of a synchronous HTTP call under the GraphQL language to the API.  
3. The API publishes the command in the topic dedicated to the aggregate to which the command belongs (eg Account). 
4. One of the µService's backend instances in charge of solving the request, obtaining the request from the aggregat-topic.  

From here two simultaneous flows are opened, one to indicate to the user that the command has been executed successfully and the other to reduce the generated domain-event and update the materialized-view.  

5a. The backend validates the conditions of the command to indicate if it is possible or not to make the change. If possible then Backend modifies the information in the materialized view and query the data after the update.  
6a. The materialized view returns the information of the aggregate after being updated.  
7a. The backend publishes the result of the command in the response-topic.  
8a. The API takes the answer from the respone-topic.  
9a. The API returns the command response in the HTTP-RESPONSE of the original request.  
10a. The front-end displays the information of the command and the aggreate data after the update to the user.  

5b. The backend validates the conditions of the command to indicate if it is possible or not to make the change. If possible then The backend publishes a domain-event indicating the action taken on the aggregate (eg AccountEnabled).  
6b. The domain-event is shared to all µServices interested in that event.  
7b. The backend of each µService updates the materialized view as indicated by the event (Eg enabling the acount). The data is updated in the differents µServices ready to be query.  

