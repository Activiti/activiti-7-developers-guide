# Activiti Cloud Audit Service

The Audit Service module provides Audit capabilities. This module is in charge of consume one or more Runtime Bundle Events and storing them into the
Event Store. Our default implementation consist of a simple JPA implementation which consume messages emitted by Runtime Bundles
and store them as they arrive. The audit service doesn't do any data manipulation.
There is also a MongoDB implementation, which makes your life easier if you want to query events that are stored in JSON format.


![](/assets/AuditService.png)

## Filters


## Implementations
 - [Activiti Cloud Audit Service - JPA (Reference )using Spring Data JPA](https://github.com/Activiti/activiti-cloud-audit/)
 - [Activiti Cloud Audit Service - Mongo DB](https://github.com/Activiti/activiti-cloud-audit-mongodb/)

## Docker Images

- [Activiti Cloud Audit Service -> JPA (Reference) Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit/)
- [Activiti Cloud Audit Service -> MongoDB Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit-mongodb/)
