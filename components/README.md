# Components

Activiti Cloud provides a set of basic building blocks which can be divided into 3 separate groups:

* [Activiti Cloud Infrastructure](activiti-cloud-infrastructure/)
* [Activiti Cloud Applications](activiti-cloud-application/)

We define a set of services that should be provided by the infrastructure, meaning that in different environments these components can be replaced by services provided by the underlaying infrastructure. Other components will rely on these infrastructural services to work, meaning that a clear set of features must be provided for the Activiti Cloud Applications to work with all the designed features.

![](../assets/infrastructure.png)

Activiti Cloud Applications are dynamic and they can be provisioned on runtime on top of an existing infrastructure. Activiti Cloud Applications can be composed using our provided building blocks to accomodate a wide range of scenarios, we provide some examples on how to compose these applications, but the Activiti Cloud design support different configurations to support large scale deployments.

![](../assets/application.png)
