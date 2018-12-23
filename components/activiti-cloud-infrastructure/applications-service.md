# Activiti Cloud Applications Service

The Application Service provides monitoring capabilities to arrange a set of Building Blocks into a logical Application. This service \(as other building blocks\) provides a Spring Boot Starter that you can add to an existing Spring Boot Application.

The Application Service Spring Boot Starter source code can be found here: [https://github.com/Activiti/activiti-cloud-app-service](https://github.com/Activiti/activiti-cloud-app-service)

The application service is in charge to interact with the Service Registry and the Centralized configuration server to understand when an application that was deployed is up an running as a logical set. Think about the Application Service as a controller for applications that are running. If services from an application goes down, the Application Service is in charge of updating the state of the whole set.

## REST endpoints

The application service expose the /v1/apps/ endpoint which answers the questions of how many applications are up and running and the state of each individual app. This service can be used in conjunction with the Gateway component to decide when to register routes for each individual application.

This service also expose a deployment descriptor directory. This directory represents the definition of which applications will be "controlled" and monitored by this service. These descriptors can be updated dynamically, for example in the centralized configuration service. Deployment Descriptors can be compared with HELM charts, but these descriptors represent a higher level abstractions of the structure of the apps. In general there will be a one to one mapping to a HHELM chart.

### Applications Endpoints

* GET /v1/apps/
* GET /v1/apps/{app\_id}
* GET /v1/apps/{app\_id}/status
* GET /v1/apps/{app\_id}/services

### Deployment descriptors Endpoint

* GET /v1/deployments/directory
* GET /v1/deployments/{app\_id}

