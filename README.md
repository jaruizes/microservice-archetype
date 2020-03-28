# Service Archetype

## Introduction
This artifact is a Maven archetype to accelerate (micro) services implementation by providing a basic structure
to implement a service

The implementation of this archetype is based on a serie of requirements and principles. 



## Requirements

The main ***requirements*** for this kind of service are:

- **REQ_001:** It must be accesible from outside by a REST API.
- **REQ_002:** It must be able to listen to events triggered from other services published to a event bus.
- **REQ_003:** It must manage its own data model and its own database engine depending on its needs.
- **REQ_004:** It must be able to publish events to an event bus.
- **REQ_005:** It must be able to invoke other services (REST, SOAP,...,etc)
- **REQ_006:** It must be tested and deployed independently.
- **REQ_007:** The operational configuration must be independent.



## Principles

Besides requirements, some principles have to be kept in mind:

- **PRINC_001:** Business logic has to be isolated of others layers in order to be developed independently.
- **PRINC_002:** Business data model musn't be shared with any other service
- **PRINC_003:** Business validations must be implemented independently API validations.
- **PRINC_004:** Business errors must be translated to API errors or messages not just thrown to outside.
- **PRINC_005:** Business logic will not listen to events directly from event bus. Some business logic is invoked in response to events.
- **PRINC_006:** Persistence logic has to be developed independently in order to be adapted to any database engine and data model
- **PRINC_007:** Persistence data model must be independent of business data model in order to evolve separately and hides persistence details to business like database engine or model
- **PRINC_008:** Persistence data model must be the most suitable to each service (SQL, normalized, de-normalized, NoSQL)
- **PRINC_009:** Business hasn't to know how to invoke an external service so that implementation has to be separated from business logic and it should be able to evolve independently.
- **PRINC_010:** Business just wants to publish an event not how to the event must be published to a concrete event bus. That logic has to be implemented separated from business logic and it should be able to evolve independently.



## Designing the service

In order to achieve the requirements and principles outlined above I'm going to design a service architecture based on **hexagonal architecture** where business domain is totally isolated. 

The idea is to implement and test business logic in a isolated way and to interact with the outside world in two ways:

- **Input API**: by defining APIs to offer business functionality to consumers (REST APIs or listening to some events)
- **Output API**: by defining some points to reach the outside world (databases, event bus or calls to others services)

The following picture shows these ideas:

![hexagonal_view](/doc/images/conceptual_view_hexagonal.png)

The next step is to translate this theorical approach based on an hexagonal architecture into a layered structure to meet the requirements and principles. To achieve this goal, I will start by establishing a conceptual view and layers, and then go step by step in more detail so that I can establish a physical structure at the end.



### Conceptual layers

Let's start with conceptual layers in order to understand how we can get a physical structure at the end. The following picture shows the conceptual layers:

![conceptual_view](/doc/images/conceptual_view_layers.png)

So, there are **three main and independent parts or layers**:

* **Input API**: this is the entry point from the outside and it could implement every kind of API: Rest, events, SOAP,...,etc. From this layer, business layer is called.
* **Business**: this the main layer of the microservice and it's where the business logic must be implemented. This layer calls to the output API in order to send events, save data into database or call to other services
* **Output API**: this is where it's placed the implementation about how to call to a database engine, how to sent an event to the event bus or how to invoke an external
service.



#### Execution Flow and dependencies between layers

The following picture shows the service execution flow:

![execution_flow](/doc/images/execution_flow.png)

According to the image, the service can be invoked by two different ways:

- Calling to its REST API
- Publishing an event to Event Bus

So, **the execution flow starts always in the API Layer**. This layer knows how to implement a Rest API or how an event must be consumed from the Event Bus used.Then, API layer calls to Business Layer in order to execute the requested operation. Sometimes API layer will wait for a response (Rest API) and sometimes not (events). 

**Business layer doesn't know the execution has been started (Rest API or events) and it doesn't care**. It just performs the logic associated to the operation requested and depending on that logic could happen different scenarios:

- Performing a database operation like insert, update, delete or query data.
- Calling to other services
- Publish an event to Event Bus

**These actions are performed in the Output API layer that knows how to perform them.** **Business layer doesn't really know those details and it doesn't care.** 

Business layer just wants to make a persistence action, calls to some service or publish some events but it doesn't know what database engine is going to be used and how information is stored there, how some service must be consumed or which is the Event Bus used and what it's necessary yo publish an event. **Details about how to access to outside world must be implemented in the Output API Layer.** 

Following these rules we'll keep Business Layer "independent" and every layer could evolve independently. The key is shown in the following picture:

![layers_dependencies](/doc/images/layers_dependencies.png)

Input API receives requests from customers (other services, UIs,...,etc) or listen to some events, process those requests and **invoke to Business Layer through Business API**. This means that Business Layer sets how Input API has to access to business logic.

Business Layer sets how business logic will call to adapters by defining business ports. Output API must implements those definitions. **If Business Layer decides to change a business port, Output API must change its implementation, not the opposite.**

Summarizing,

- the execution flow starts from Input API, goes through Business Layer and ends in Output API
- Business Layer sets how Input API must call it and also the interface that Output API adapters must implement in order to be invoked from business logic

This allows that Business Layer "has no logical dependencies" with the rest of layers.



#### Testing strategy

When you are designing an software piece it's very important to think how you are going to test that piece. A good design allows good testing.

As we saw in the previous sections, each layer has a specific responsibility and it should be independently tested, mocking the layer on which it depends. That means:

![unit_testing_strategy](/doc/images/unit_testing_strategy.png)

With this "unit testing" we'll check if each layer does what it should do but we also has to check if all of them together do what the service should do. So, we will also have to implement a "end-to-end" tests to check service use cases:

![e2e-service-tests](/doc/images/e2e-service-tests.png)



### Logical view

We've already design a conceptual view in order to have a high level point of view of the service and its conceptual components, checking if we're covering requirements and principles. 

Now, we have to go deeper and decomponse each layer in logical components. Let's see how each layer is composed:

![logical_view](/doc/images/logical_view.png)



## How to use

TBC

### Local installation
The first step is to install the archetype in your local maven repository. To do this, execute:

```
mvn install
```

### Use the archetype to generate a project
To generate a project from the archetype, execute this command:
```
mvn archetype:generate -DarchetypeGroupId=com.jaruizes.jarcmazon.archetypes -DarchetypeArtifactId=service-archetype -DarchetypeVersion=1.0.0
```

Then, the system will ask us:

- service groupId
- service artifactId
- service version (default 1.0-SNAPSHOT)
- service package (default the same value as groupId)

And we will have to confirm the values. Then, the service structure will be created in a folder named as artifactId value.

Example:

```
> mvn archetype:generate -DarchetypeGroupId=com.jaruizes.jarcmazon.archetypes -DarchetypeArtifactId=service-archetype -DarchetypeVersion=1.0.0

[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< org.apache.maven:standalone-pom >-------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
[INFO] 
[INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
[INFO] 
[INFO] 
[INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
[INFO] Generating project in Interactive mode
[INFO] Archetype repository not defined. Using the one from [com.jaruizes.jarcmazon.archetypes:service-archetype:1.0.0] found in catalog local
Define value for property 'groupId': com.jaruizes.jarcmazon.sales 
Define value for property 'artifactId': sales-service
Define value for property 'version' 1.0-SNAPSHOT: : 1.0.0-SNAPSHOT
Define value for property 'package' com.jaruizes.jarcmazon.sales: : 
Confirm properties configuration:
groupId: com.jaruizes.jarcmazon.sales
artifactId: sales-service
version: 1.0.0-SNAPSHOT
package: com.jaruizes.jarcmazon.sales
 Y: : 
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: service-archetype:1.0.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.jaruizes.jarcmazon.sales
[INFO] Parameter: artifactId, Value: sales-service
[INFO] Parameter: version, Value: 1.0.0-SNAPSHOT
[INFO] Parameter: package, Value: com.jaruizes.jarcmazon.sales
[INFO] Parameter: packageInPathFormat, Value: com/jaruizes/jarcmazon/sales
[INFO] Parameter: package, Value: com.jaruizes.jarcmazon.sales
[INFO] Parameter: groupId, Value: com.jaruizes.jarcmazon.sales
[INFO] Parameter: artifactId, Value: sales-service
[INFO] Parameter: version, Value: 1.0.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /Users/joalruiz/Development/Personal/Jarcmazon/infrastructure/sales-service
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  41.788 s
[INFO] Finished at: 2020-03-26T16:18:40+01:00
[INFO] ------------------------------------------------------------------------
```
