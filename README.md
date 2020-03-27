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
- **PRINC_002:** Business logic musn't have dependencies with others layers in order to be developed independently.
- **PRINC_003:** Business data model musn't be shared with any other service
- **PRINC_004:** Business validations must be implemented independently API validations.
- **PRINC_005:** Business errors must be translated to API errors or messages not just thrown to outside.
- **PRINC_006:** Business logic will not listen to events directly from event bus. Some business logic is invoked in response to events.
- **PRINC_007:** Persistence logic has to be developed independently in order to be adapted to any database engine and data model
- **PRINC_008:** Persistence data model must be independent of business data model in order to evolve separately and hides persistence details to business like database engine or model
- **PRINC_009:** Persistence data model must be the most suitable to each service (SQL, normalized, de-normalized, NoSQL)
- **PRINC_010:** Business hasn't to know how to invoke an external service so that implementation has to be separated from business logic and it should be able to evolve independently.
- **PRINC_011:** Business just wants to publish an event not how to the event must be published to a concrete event bus. That logic has to be implemented separated from business logic and it should be able to evolve independently.



## Service Architecture

In order to achieve the requirements exposed below, the archetype generates a base service structure based on an hexagonal architecture principles. 

I'm going to explain the desing steps I follow until getting an implementation.



### Conceptual view

Let's start with the "conceptual view":

![conceptual_view](/doc/images/conceptual_view.png)

So, there are **three main and independent parts**:

* **Input API**: this is the entry point from the outside and it could implement every kind of API: Rest, events, SOAP,...,etc. From this layer, business layer is called.
* **Business**: this the main layer of the microservice and it's where the business logic must be implemented. This layer calls to the output API in
order to send events, save data into database or call to other services
* **Output API**: this is where it's placed the implementation about how to call to a database engine, how to sent an event to the event bus or how to invoke an external
service.

#### Testing strategy

When you are designing an software piece it's very important think how you are going to test that piece in order to guarantee a good quality. 

In this case, each layer has a specific responsibility and it should be independently tested, mocking the layer on which it depends. That means:

![unit_testing_strategy](/doc/images/unit_testing_strategy.png)



### Logical view

Let's see how each layer is composed:



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
