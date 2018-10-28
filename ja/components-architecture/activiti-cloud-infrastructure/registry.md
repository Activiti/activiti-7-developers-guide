# Registry

We are relying on a Service Registry to register each of our services to the platform. In our Docker Compose setup we are relying on Netflix Eureka to dynamically register our Services at bootstrap time. For Kubernetes we are looking into using the Spring Cloud DiscoveryClient to work against the kubernetes registry. Meaning that Eureka might not be present in our Kubernetes deployments.

## Source Code & Docker Image

* [Service Registry -&gt; Source Code](http://)
* [Service Registry -&gt; Docker Image](http://)

