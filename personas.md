# Personas

It is important to understand our key use-cases and which personas we are targeting with each of our tools. This is a conceptual tool to help focus on how the system can be used (we know that in the real world one individual might perform multiple persona-roles). The following chart shows the main Personas that we target and which features they can use to perform their work.

![](/assets/Activiti-Cloud-Personas.png)

Activiti 7 & Activiti Cloud will first target Activiti Developers & Activiti DevOps which are in charge of setting up the infrastructure and making sure that Activiti Applications can be deployed to a cloud provider.

## Activiti Cloud Developers
Activiti Cloud Developers are responsible for building System to System connectors which will serve as integration points with existing internal/external systems.
The Process Engine (inside our Runtime Bundles) will have no knowledge about these connectors and the integration will happen via Events. These Cloud Connectors will be discovered on runtime and they will use the infrastructure to provide failover and fallback mechanisms. Developers will be in charge of testing these connectors, package them and release them (Maven/Docker). Developers will be also in charge of testing business processes definitions with the required connectors.

## Activiti Cloud DevOps
Activiti Cloud DevOps are responsible for picking up released Runtime Bundles and Cloud Connectors and deploying them into an infrastructure instance.
DevOps will pick up only released versions which have been tested by Developers and released to a certain repository (Maven/Docker) which DevOps have access.
