# Personas

It is important to understand which personas we are targeting with our different set of tools. For that reason, the following chart shows the main Personas that we will target and which features they will be using to perform their work.

![](../.gitbook/assets/activiti-cloud-personas.png)

Activiti Cloud will first target Activiti Developers & Activiti DevOps which are in charge of setting up the infrastructure and making sure that Activiti Applications can be deployed to a cloud provider.

## Activiti Cloud Developers

Activiti Cloud Developers are responsible for building System to System connectors which will serve as integration points with existing internal/external systems. The Process Engine \(inside our Runtime Bundles\) will have no knowledge about these connectors and the integration will happen via Events. These Cloud Connectors will be discovered at runtime and they will use the infrastructure to provide failover and fallback mechanisms. Developers will be in charge of testing these connectors, packaging and releasing them \(Maven/Docker\). Developers are also in charge of testing business process definitions in addition to the required connectors. After testing, Developers will be in charge of releasing these Runtime Bundles and Connectors.

## Activiti Cloud DevOps

Activiti Cloud DevOps are responsible for picking up released Runtime Bundles and Cloud Connectors and deploying them into an instance of our infrastructure. DevOps will only have access to and be able to pick up released versions which Developers have tested and released to a specific repository \(Maven/Docker\).

## Tooling and integrations

For both Developers and DevOps personas, the Activiti Cloud team is always looking for tools to simplify their daily work. We have found the following tools to be extremely useful:

* [Jenkins X](http://jenkins-x.io): CI/CD done right for Kubernetes
* [JHipster](http://jhipster.tech): Spring Cloud and Angular/React generators
* [HELM](http://helm.sh/): Kubernetes Package Manager, we use it for doing deployments of Activiti Cloud applications in K8s.
* [Spring Cloud Kubernetes](http://github.com/spring-cloud/spring-cloud-kubernetes/): we use it to avoid duplication/overlap with the infrastructure
* [Istio](http://istio.io): Service Mesh on top of K8s
* [KNative](http://knative.dev/): Function as a Service layer on top of K8s

