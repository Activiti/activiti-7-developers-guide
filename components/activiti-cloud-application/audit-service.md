# Activiti Cloud Audit Service

The Audit Service module provides Audit capabilities. This module is in charge of consuming one or more Runtime Bundle Events and storing them into the Event Store. Our default implementation consists of a simple JPA implementation which consumes messages emitted by Runtime Bundles and store them as they arrive. The audit service doesn't do any data manipulation. There is also a MongoDB implementation, which makes your life easier if you want to query events that are stored in JSON format.

![](../../.gitbook/assets/auditservice.png)

## Specification

The core business logic for the Audit Service module can be found [here](https://github.com/Activiti/activiti-cloud-audit-service). You can take these implementations as the specification for the Audit component and use them as the base for your own custom implementation. We recognise that different use cases might require different technologies to perform under different circumstances. For that reason we provide a reference implementation that does the work, but we promote alternative approaches when needed. If you are interested in contributing an alternative or extensions for our reference implementations we encourage you to get in touch.

## Parameters

Queries to REST endpoints can be restricted using parameters to match data fields. For example:

`/audit/v1/events?processInstanceId={processInstanceId}` `/audit/v1/events?eventType={eventType}`

## Security and Permissions

By default only admin users can access everything. The endpoints that begin with /admin/ can be opened up by passing the role 'user' as the value of the ACTIVITI\_ADMIN\_ROLE environment variable.

Event data can be restricted to particular users or roles. This can be done by adding properties such as:

activiti.cloud.security.user.testuser.rb-app-name.policy.read=defKey1 activiti.cloud.security.user.hruser.rb-app-name.policy.read=defKey2

Or a wildcard '\*' can be used.

Here rb-app-name is the name of a runtime bundle application. Or environment variables can be used such as

`ACTIVITI_CLOUD_SECURITY_USER_HRUSER_RBMYAPP_POLICY_READ=SimpleProcess` `ACTIVITI_CLOUD_SECURITY_GROUP_HR_RBMYAPP_POLICY_WRITE=SimpleProcess` `ACTIVITI_CLOUD_SECURITY_GROUP_TESTGROUP_RBMYAPP_POLICY_WRITE=*`

## Spring Boot Starters

You can find the Spring Boot 2.x starters used to create our Docker Images and Spring Boot enabled services in the following repository: [Activiti Cloud Audit Services & Spring Boot Starters](https://github.com/Activiti/activiti-cloud-audit-service)

## Implementations

* [Activiti Cloud Audit Service - JPA \(Reference \)using Spring Data JPA](https://github.com/Activiti/activiti-cloud-audit/)
* [Activiti Cloud Audit Service - Mongo DB](https://github.com/Activiti/activiti-cloud-audit-mongodb/)

## Docker Images

* [Activiti Cloud Audit Service -&gt; JPA \(Reference\) Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit/)
* [Activiti Cloud Audit Service -&gt; MongoDB Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-audit-mongodb/)

