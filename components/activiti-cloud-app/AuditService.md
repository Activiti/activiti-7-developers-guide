# Activiti Cloud Audit Service

The Audit Service module provides Audit capabilities. This module is in charge of consume one or more Runtime Bundle Events and storing them into the
Event Store. Our default implementation consist of a simple JPA implementation which consume messages emitted by Runtime Bundles
and store them as they arrive. The audit service doesn't do any data manipulation.
There is also a MongoDB implementation, which makes your life easier if you want to query events that are stored in JSON format.


![](/assets/AuditService.png)

## Specification

The core business logic for the Audit Service module can be found [here](https://github.com/Activiti/activiti-cloud-audit-service/tree/develop/activiti-cloud-services-audit). You can take these implementations as the specification for the Audit component and use them as the base for your own custom implementation. We recognise that different use cases might require different technologies to perform under different circumstances. For that reason we provide a reference implementation that does the work, but we promote alternative approaches when needed.
If you are interested in contributing an alternative or extensions for our reference implementations we encourage you to get in touch.

## Filters


## Spring Boot Starters
You can find the Spring Boot 2.x starters used to create our Docker Images and Spring Boot enabled services in the following repository:
[Activiti Cloud Audit Services & Spring Boot Starters](https://github.com/Activiti/activiti-cloud-audit-service)

## Implementations
 - [Activiti Cloud Audit Service - JPA (Reference )using Spring Data JPA](https://github.com/Activiti/activiti-cloud-audit/)
 - [Activiti Cloud Audit Service - Mongo DB](https://github.com/Activiti/activiti-cloud-audit-mongodb/)

## Docker Images

- [Activiti Cloud Audit Service -> JPA (Reference) Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit/)
- [Activiti Cloud Audit Service -> MongoDB Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit-mongodb/)
