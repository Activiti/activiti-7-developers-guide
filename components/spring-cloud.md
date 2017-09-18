# Spring Cloud + Activiti Cloud
From the Activiti framework point of view we rely in 3 key aspects from Spring Boot / Spring Cloud which enable our components (such as the process engine) to integrate nicely with the rest of the infrastructure.

First of all we rely on Spring Boot HATEOAS for all of our REST endpoints making sure that they are aligned and we have minimal code to maintain.

Secondly, we rely heavily on Spring Cloud Stream to make sure that we can consume and emit events in an asynchronous manner. This is a key enabler for making sure that independent components can collaborate without being tightly coupled.

Finally, we leverage the abstraction layers on top of several services such as Service Registry, Gateway for dynamic service registration, discovery and routing, Distributed Configuration Service, etc.

We leverage all the components to make sure that we run nicely in distributed environments with technologies such as Docker and Kubernetes. We are also making sure that Activiti Cloud doesn’t overlap with any of the features provided by such technologies to not cause any mismatch. 
