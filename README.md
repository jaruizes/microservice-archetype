# Service Archetype

## Introduction
This artifact is a Maven archetype to accelerate (micro) services implementation by providing a basic structure
to implement a service

## Service Architecture
The archetype generates a base service structure based on an hexagonal architecture principles:

### Conceptual view

![conceptual_view](/doc/images/conceptual_view.png)

So, there are three main parts:

* **Input API**: this is the entry point from the outside. From this layer, business layer is called.
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
