# 12 Factor Apps

To build Cloud Native Applications and Services, it is not enough to package the old monolith inside a Docker Image and run it inside Kubernetes. We value the principles that Heroku defined called "The Twelve-Factor App": [https://12factor.net](https://12factor.net) \(and from a Pivotal perspective see [https://content.pivotal.io/ebooks/beyond-the-12-factor-app](https://content.pivotal.io/ebooks/beyond-the-12-factor-app) \). Without these guidelines, it becomes difficult to scale in distributed environments. Activiti Cloud repositions the process engine to better interact with other components in such distributed environments. The measure of success for Activiti Cloud is to have a low impedance mismatch with other microservices and the way they are designed, built and deployed.

## \#1 - One codebase, one application

Our examples services are all under different repositories and each represents a single Spring Boot application which is also enabled with Spring Cloud libraries. Each of these services repositories contains a set of artifacts that makes them suitable for CI/CD pipelines:

* **Jenkinsfile**: \(or other pipiline definition\)pipeline to build, deploy and promote the current service to a Kubernetes Cluster.
* **Maven project**: to define the service built with Spring Boot and Activiti Cloud Starters.
* **Dockerfile**: to define how to build a docker image for the service
* **HELM Charts**: to define the set of manifests \(kubernetes descriptors\) to deploy the service into a running Kubernetes Cluster.

We provide for each building block a Spring Boot Starter that can be customized and extended for your domain specific requirements.

Our Spring Boot Starters can be found here:

* [Activiti Cloud Runtime Bundle](https://github.com/Activiti/activiti-cloud-runtime-bundle-service)
* [Activiti Cloud Query Service](https://github.com/Activiti/activiti-cloud-query-service)
* [Activiti Cloud Audit Service](https://github.com/Activiti/activiti-cloud-audit-service)
* [Activiti Cloud Connectors Service](https://github.com/Activiti/activiti-cloud-connectors)

## \#2 - API first

REST and Message Driven APIs are defined for each service, and a specification for each can be found in the component documentation. We want to make sure that the contract is well defined so different implementations can be supported. The objective behind supporting different implementations \(based on different technologies\) enable us to support a wider range of scenarios \(low latency, data intensive, your custom requirement, etc.\)

## \#3 - Dependency Management

Activiti Core and Activiti Cloud dependencies are managed using Bill of Materials \(BoMs\) which centralize the services and libraries dependencies. These BoMs can be found in these two repositories:

* [Activiti Core Dependencies](https://github.com/Activiti/activiti-dependencies)
* [Activiti Cloud Dependencies](https://github.com/Activiti/activiti-cloud-dependencies)

## \#4 - Design, build, release, run

* **Design**: we keep a closed loop between design, RFC and coding to make sure that we rapidly build small features that improve our services and can frequently be released \(at least once a month\).
* **Build**: Our CI servers build each of our services and release to Alfresco Nexus and Maven central so you can depend on frequently released artefacts. All of our services can be configured using environment variables so we keep them immutable and you can change their configuration using standard container practices
* **Release**: for domain-specific modules/services we provide mechanisms to make sure that you can reproduce your releases to be able to audit and troubleshoot when things go wrong
* **Run**: we provide hierarchical HELM charts that enable simple deployment of both infrastructure and applications. You can find how to get started with these HELM charts in our Getting Started Guides.

## \#5 - Configurations, credentials and code

Configurations for all Activiti Cloud services are provided by env variables and the use of Spring Cloud configuration service is available for system/platform-wide configurations. These configuration can be divided by environments and by using Secrets from Kubernetes we enable integration with other secret provider. 

## \#6 - Logs

Activiti Cloud services' logs are delegated to the infrastructure. You can hook up your ELK stack to analyse and monitor your logs. Environment variables are used to configure where the logs are written. We want to rely on common tools for monitoring and logging instead of providing our own custom solution for this. 

## \#7 - Disposability

Activiti Cloud services are designed to start fast, under 30 seconds, so they can be quickly scaled as needed. These services do not store state so they can be disposed of and recreated at any time by the infrastructure. The Activiti Cloud team is committed to improve startup times and to make sure that up front operations are kept to the minimum.

## \#8 -  Backing services

Activiti Cloud services use the concept of bounded resources for security, email, data sources, storage and other resources. It also uses the service registry to abstract where other services are and how they communicate with each other.

## \#9 - Environment parity

Activiti Cloud provides to our users and implementers a production like environment to produce their domain-specific artefacts that can be published later on to production environments. Activiti Cloud relies on industry standards to make sure that dealing with multiple environments for development, testing/QA and production is as easy and real as possible. We rely on technologies such as HELM and GitOps practices to make sure that these environments can be easily reproduced across different clusters.

## \#10 - Administrative processes

In Activiti Cloud administrative processes originally bundled along with the process engine are being refactored outside to reuse services provided by the infrastructure. Administrative Processes are one of the main problems in most Open Source BPM projects. For this reason, we are working hard to get each of these processes out of the Process Engine scope.

## \#11 - Port bindings

All Activiti Cloud services are Spring Boot applications that allow environment variables to configure their name \(for auto-discovery\) and their port where they will run to serve as backing services to other applications and services. All our services run in the same port, and they leverage Kubernetes networking to do the mappings and bindings.

## \#12 - Stateless Process

Activiti Cloud services are stateless by design, and if they require to store state, they will bind to a data source to store it. Caching mechanisms \(such as Redis\) will be used to share state when needed.

## \#13 - Concurrency

Runtime Bundles were designed with the idea of scaling process execution - if there are too many requests to create process instances of a type or too many interactions against a specific set of process instances, new runtime bundle instances can be created on demand to handle the load. No extra configuration is required to achieve this scalability in Activiti Cloud - it is an innate feature of the platform.

## \#14 - Telemetry

Activiti Cloud services provide telemetry by standard Spring Boot (micrometer) actuators and health indicators, but it also provides business level telemetry my emitting a standardised set of events that expose the runtime operations. All this information can be used for data warehousing, reporting  and forecasting purposes.

## \#15 - Authentication and Authorization

Activiti Cloud relies on SSO and IDM for all its services. RBAC \(role-based access control\) is a native part of all our services. Activiti Cloud components will provide hook points for domain specific extensions to build more complex use cases.

