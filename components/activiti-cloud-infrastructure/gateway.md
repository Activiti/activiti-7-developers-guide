# Gateway

From the clients point \(User Interfaces, other applications\) of view it is quite common to expose a single entry point to interact with all the services. The Gateway component provides this single entry point to access all the services that are up and running. The gateway is configured to register all the services which becomes available inside the namespace where the gateway is deployed.

The Activiti Cloud Gateway uses the new Spring Cloud Gateway based on the Reactive framework in conjuctions with [Spring Cloud Kubernetes](https://spring.io/projects/spring-cloud-kubernetes#overview) for Service Discovery and configurations. The gateway automatically register routes for each discovered service inside the Kubernetes Service Registry.

* [Application Gateway -&gt; Source Code](https://github.com/activiti/activiti-cloud-gateway)
* [Application Gateway -&gt; Docker Image](https://cloud.docker.com/u/activiti/repository/docker/activiti/activiti-cloud-gateway)

