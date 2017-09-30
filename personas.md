# Personas

It is important to understand which personas we are targeting with our different set of tools. For that reason, the following chart shows the main Personas that we will target and which features they will be using to perform their work.

![](/assets/Activiti-Cloud-Personas.png)

Activiti 7 & Activiti Cloud will first target Activiti Developers & Activiti DevOps which are in charge of setting up the infrastructure and making sure that Activiti Applications can be deployed to a cloud provider.

## Activiti Cloud Developers
Activiti Cloud Developers are responsible for building System to System connectors which will serve as integration points with existing internal/external systems.
The Process Engine (inside our Runtime Bundles) will have no knowledge about these connectors and the integration will happen via Events. These Cloud Connectors will be discovered on runtime and they will use the infrastructure to provide failover and fallback mechanisms. Developers will be in charge of testing these connectors, package them and release them (Maven/Docker). Developers will be also in charge of testing business processes definitions in addition with the required connectors. After testing Developers will be in charge of releasing these Runtime Bundles.

## Activiti Cloud DevOps
Activiti Cloud DevOps are responsible for picking up released Runtime Bundles and Cloud Connectors and deploy them into an instance of our infrastructure.
DevOps will pick up only released versions which have been tested by Developers and released to a certain repository (Maven/Docker) which DevOps have access.
