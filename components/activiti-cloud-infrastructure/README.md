# Activiti Cloud Infrastructure

Activiti Cloud has been designed to run inside Kubernetes. This means that Activiti Cloud will interact with Kubernetes native services such as the Kubernetes Service Registry, the Config Maps for configuration, secrets, Jobs/CronJobs, etc.

From the infrastructure point of view, we will deploy inside Kubernetes the following set of services:

* **Gateway**: from the clients point \(User Interfaces, other applications\) of view it is quite common to expose a single entry point to interact with all the services. The Gateway component provides this single entry point to access all the services that are up and running. The gateway is configured to register all the services which becomes available inside the namespace where the gateway is deployed by default, but we recommend the use of the Application Service Operator to expose applications as logical units.
* **Identity Management / Single Sign On**: provides Single Sign On capabilities for all the services that are running, so we can propagate the JWT for Authorization and Authentication purposes. This service also provide Identity Management capabilties which allows us to sync users from LDAP or AD servers. 
* **Distributed Logging / Tracing / Monitoring**: if you are working with distributed systems you need to have a way to monitor, trace and handling loging for all the services. 

