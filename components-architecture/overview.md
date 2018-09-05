# Overview

Activiti Cloud provides a set of basic building blocks part of them are infrastructural services that will enable the whole platform to work and part of them are BPM specific. All these components were designed to be scalable independently one from the other and you are free to re-arrange them or replace them with your existing infrastructural services.

The following components are currently being developed:

* Activiti Cloud Infrastructure
  * [Activiti Cloud Gateway](activiti-cloud-infrastructure/gateway.md)
  * [Activiti Cloud Registry](activiti-cloud-infrastructure/registry.md)
  * [Activiti Cloud Single Sign On \(SSO\)](activiti-cloud-infrastructure/sso.md)
  * [Activiti Cloud Identity Management \(IDM\)](activiti-cloud-infrastructure/idm.md)
  * [Activiti Cloud Configuration Server](activiti-cloud-infrastructure/config.md)
  * [Activiti Cloud Logging](activiti-cloud-infrastructure/logging.md)
  * Storage / Databases
  * Message Brokers
* Activiti Cloud Applications
  * [Activiti Cloud Query Service](activiti-cloud-application/queryservice.md)
  * [Activiti Cloud Audit Service](activiti-cloud-application/auditservice.md)
  * [Activiti Cloud Notification Service](https://github.com/Activiti/activiti-7-developers-guide/tree/c19bf319b907e1dcbc6be1d9b71680199fbd17c2/components/activiti-cloud-app/NotificationService.md)
  * [Activiti Cloud Runtime Bundles](activiti-cloud-application/runtimebundle.md)
  * [Activiti Cloud Connectors](activiti-cloud-application/cloudconnectors.md) 

We define a set of services that should be provided by the infrastructure, meaning that in different environments these components can be replaced by services provided by the underlaying infrastructure. Other components will rely on these infrastructural services to work, meaning that a clear set of features must be provided for the Activiti Cloud Applications to work with all the designed features.

![](../.gitbook/assets/infrastructure.png)

Activiti Cloud Applications are dynamic and they can be provisioned on runtime. We use runtime bundles to define application scopes. These Runtime Bundles encapsulates the Process Runtime and a fixed set of business processes \(and versions\) that we want to automate.

Following this approach, we will have two different Runtime Bundles if we decide to change one or more versions of our business processes.

Each of these Activiti Cloud Applications can define where to store the runtime state of its processes. This allows extreme flexibility on configuration to support a wide range of scenarios, but it also delegates the responsibility to the architects defining how the implementation will work. In order to simplify these decisions, sensitive defaults will be provided.

![](../.gitbook/assets/application.png)

You can find examples and tutorials on how to use these services for your applications .

You can also find  an example scenario using these technology.

