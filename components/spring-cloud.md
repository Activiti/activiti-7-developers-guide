# Spring Cloud

From the Activiti framework point of view we rely in 3 key aspects from Spring Boot / Spring Cloud / Spring Cloud Kubernetes which enable our components \(such as the Process Runtime\) to integrate nicely with the rest of the infrastructure.

First of all we rely on Spring Boot HATEOAS for all of our REST endpoints making sure that they are aligned and we have minimal code to maintain. We also provide an alternate format for the JSON payloads to follow the Alfresco API guidelines. Spring Boot is our weapon of choice to create our microservices. We rely on Spring Boot to provide the basic set of frameworks tested together to create REST endpoints and Dependency Injection to bootstrap our services. We heavily rely on the Spring Boot Starter concept, where we use @EnableAutoConfiguration's to create all the beans that will be required in runtime. These AutoConfigurations also help us to provide configurations points to parameterize the behaviour of our provided services.

Secondly, we rely heavily on Spring Cloud Stream to make sure that we can consume and emit events in an asynchronous manner. This is a key enabler for making sure that independent components can collaborate without being tightly coupled. Spring Cloud Streams support the concepts of Binders which allow us to use different implementations of the underlaying Message Brokers such as: RabbitMQ, Kafka, Amazon SQS, etc.

Finally, we leverage the abstraction layers on top of several services such as Service Registry, Gateway for dynamic service registration, discovery and routing, Distributed Configuration Service, etc. To leverage these capabitiles inside Kubernetes we use the Spring Cloud Kubernetes project which has been included in the Spring Cloud release train. By using Spring Cloud Kubernetes we can integrate our services to Kubernetes native services to levarage Service Discovery, Configurations, Secrets, etc.

We leverage all the components to make sure that we run nicely in distributed environments with technologies such as Docker inside Kubernetes. We are also making sure that Activiti Cloud doesn’t overlap with any of the features provided by such technologies to not cause any friction.

