# Previous Versions of Activiti

## Feature Changes

The heart of the Activiti projects is the process engine and the engine in v7 is fundamentally much the same as in v6. The major difference is that v7 provides new ways to use it. We know that Activiti has been great for lots of non-cloud projects and we want it to continue to be (whichever version you use), as well as now becoming great for cloud projects too.

Users who are used to the spring-boot-starter-basic (see http://www.baeldung.com/spring-activiti) will find the same possibilities in activiti-cloud-starter-configure. In addition Activiti Cloud also provides new starters to support bootstrapping a set of microservices to quickly create a scalable cloud architecture.

In order to separate identity management functions from the core engine, users and groups management is no longer provided by the engine in v7. A migration tool to port users and groups to keycloak is provided https://github.com/Activiti/activiti-compatibility/tree/master/activiti-db-users-to-keycloak-migrator

Activiti v7 provides new REST APIs to make use of new possibilities such as HATEOS and more dynamic querying using DSLs. But the old API is not forgotten - https://github.com/Activiti/activiti-compatibility/tree/master/activiti-rest

## Maintenance of Old Versions

Activiti 6 is being actively maintained and we will review pull requests on previous versions on a case-by-case basis.