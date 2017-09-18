# Beyond the 12 Factor Application

In order to build Cloud Native Applications and Services, it is not enough to just package the old monolith inside a Docker Image and run it inside Kubernetes. For that reason, based on what Heroku defined as 12 factor applications: [https://12factor.net](https://12factor.net), and what Spring Pivotal's engineer reviewed here: [https://content.pivotal.io/ebooks/beyond-the-12-factor-app](https://content.pivotal.io/ebooks/beyond-the-12-factor-app). We are building Activiti Cloud following these principles. Without these guidelines it becomes really difficult to scale in distributed environments. At its core, Activiti Cloud is pushing the BPM industry out of their confort zone, making  changes in how the process engine is designed to interact with other components in such distributed environments.  The measure of success for Activiti Cloud is to have a low impedance mismatch with other microservices and they way they are design, built and deployed. 

## \#1 - One codebase , one application

Our services are all under different repositories and each represent a single spring boot application.

## \#2 - API first

Rest and Message Driven APIs are defined at each service and a specification of each of them can be found at the component documentation. 

## \#3 - Dependency management

Activiti and Activiti Cloud dependencies are managed in a central repository to have a clear understanding g of which underlying frameworks versions are used.

## \#4 - Design, build, release, run

* **Design**: we keep a close loop of design, RFC and coding to make sure that we build small features and improve our services that can be released frequently \(at least once a month\).
* **Build**: each of our services is built by our CI servers and released to alfresco nexus and Maven central so you can depend on frequently released artefacts and nightly builds. If you work against nightly builds of our services and projects while you are developing your apps you helps us to make sure that everything will work for you when you release your apps/services. Stop fearing SNAPSHOTS. All of our services can be configured using environment variables so we keep them immutable and you can change their configuration using standard containers practices
* **Release**: for domain specific modules/services we will provide mechanisms to make sure that you can reproduce your releases to be able to audit and troubleshoot when things go wrong
* **Run**: we provide both, docker compose and a mini Kube setup so you can run all the infrastructure and services locally for development purposes

## \#5 - Configurations, credentials and code

For Activiti cloud services all the configurations are provided by env variables and the use of spring cloud configuration service is available for system/platform wide configurations.

## \#6 - Logs

Logs of Activiti cloud services are delegated to the infrastructure, you can hook up your ELK stack to analyse and monitor your logs. This configuration about where the logs will go is done via env variables.

## \#7 - Disposability

Activiti Cloud services are designed to start fast, under 30 seconds so they can be scaled quickly when needed. These services do not store state so they can be disposed and recreated at anytime by the infrastructure. The Activiti Cloud team is committed to improve startup times and to make sure that up front loaded operations to the minimum.

## \#8 -  Backing services

Activiti Cloud services use the concept of bounded resources for security, email, data sources, storage and other resources. It also uses the service registry to abstract where other services are and how they communicate to each other.

## \#9 - Environment parity 

Activiti Cloud provides to our users and implementers a production like environment to produce their domain specific artefacts that can be published later on to production environments. Activiti cloud relies on industry standards to make sure that dealing with multiple environments for development, testing/QA and production is easy and as real as possible.

## \#10 - Administrative process

In Activiti Cloud administrative processes originally bundled along with the process engine are going to be refactored outside to reuse services provided by the infrastructure. Administrative Processes are one of the main problems in most Open Source BPM projects, for this reason, we are working hard to get each of these process out of the Process Engine scope.  

## \#11 - Port bindings

All Activiti Cloud services are spring boot applications that allow environment variables to configure their name \(for auto discovery\) and their port where they will run to serve as backing services to other applications and services.

## \#12 - Stateless Process

Activiti Cloud services are stateless by design, if they require to store state  they will bind to a data source to store it. Caching mechanisms \(such as redis\) will be used to share state when needed.

## \#13 - Concurrency

Runtime bundles were design with the idea of scaling processes executions in mind, if there are too many requests to create process instances of a type or too many interactions against a certain set of process instances, new runtime bundle instances can be created on demand to handle the load. No extra configuration is required to achieve this scalability in Activiti Cloud, it is an innate feature of the platform.

## \#14 - Telemetry

Activiti cloud services provides telemetry by standard spring boot actuators and health indicators, but it also provides more business level telemetry my emitting a standardised set of events that expose the engine operations. All this information in conjunction can be used for data warehousing and forecasting purposes.

## \#15 - Authentication and Authorization 

Activiti cloud relies on SSO and IDM for all its services. RBAC \(role based access control\) is a native part of all our services. 



