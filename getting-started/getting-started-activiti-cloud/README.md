# Getting Started - Activiti Cloud

## Getting Started with Activiti Cloud

Activiti Cloud is a set of Cloud Native components designed from the ground up to work in distributed environments. We have chosen Kubernetes as our main deployment infrastructure and we are using Spring Cloud / Spring Boot along with Docker for containerization of these components.

We have gone through a very valuable journey, meeting very passionate developers, communities and existing and potential customers who are looking to leverage these technologies \(and business automation solutions\) to reduce time to market and improve business agility in the Cloud. We have also contributed with these communities, making sure that the Open Source projects that we consume get back our valuable feedback and contributions.

Activiti Cloud includes 4 foundational building blocks:

* Activiti Cloud Runtime Bundle
* Activiti Cloud Query
* Activiti Cloud Audit
* Activiti Cloud Connectors

These building blocks are Spring Boot Starters that can be attached to any Spring Boot \(2.x\) application. These building blocks are enhanced with Spring Cloud functionalities which provide the Cloud Native capabilities.

By using these components you can create Activiti Cloud applications that:

* can be scaled independently based on demand
* can be managed in completely isolated cycles
* can be upgraded and maintained independently
* can provide domain specific features using the right tool for the job

On this tutorial, we wanted to show how to get started by deploying an example set of these building blocks in Kubernetes. We strongly recommend having a real Kubernetes Cluster such as GKE, PKS or EKS. We have tested the content of this blog post in AWS \(Using Kops, PKS, GKE and also with Jenkins X\)

Let’s get started with Kubernetes, HELM and Activiti Cloud.

## Prerequisites

The quickest and easiest way to deploy things to Kubernetes is by using HELM charts. HELM, as described in the official documentation, is: “_a tool that streamlines installing and managing Kubernetes applications. Think of it like apt/yum/homebrew for Kubernetes_.”

As part of Activiti Cloud, we have created a set of hierarchical HELM charts that can be used to deploy several components, some related to infrastructure \(such as SSO and Gateway\) and some Application specific components like Runtime Bundle, Audit Service, Query Service and a Cloud Connector.

These HELM charts can be found here: [https://github.com/Activiti/activiti-cloud-charts](https://github.com/Activiti/activiti-cloud-charts).

In this quick-start, we will be looking more specifically at: [https://github.com/Activiti/activiti-cloud-charts/tree/master/activiti-cloud-full-example](https://github.com/Activiti/activiti-cloud-charts/tree/master/activiti-cloud-full-example)

This “Activiti Cloud Full Example” deploys the following components:

![Activiti Cloud application full example chart](../../.gitbook/assets/activiti-cloud-full-example-chart.png)

One important thing to notice is that each of the Activiti Cloud components can be used independently. This example is intended to show a large-scale deployment scenario. You can start small with a Runtime Bundle \(which provides the process and task runtimes\), but if you want to scale things up you need to know what you are aiming for, and this charts shows you exactly that.

### Install Kubectl and HELM

* Kubectl : [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
* HELM: [https://docs.helm.sh/using\_helm/\#installing-helm](https://docs.helm.sh/using_helm/#installing-helm)

In the next section, we show you how to create a Kubernetes cluster using Amazon Web Services EKS or Google Cloud Platform GKE. We let you decide which cloud platform best suits you. You can also deploy the Activiti Cloud full example on you local machine using for example Docker Desktop. We recommend using a cloud infrastructure for a faster and smoother experience but if you need a local install you can check [our blog post series here](https://community.alfresco.com/community/bpm/blog/2018/12/10/getting-started-with-activiti-7-beta#jive_content_id_Deploying_and_Running_a_Business_Process).

## Steps 1 and 2: Create a K8 cluster and deploy Activiti Cloud full example

### [Option A: Using Amazon EKS ](amazon-eks.md)

### [Option B: Using Google Cloud - GKE](google-cloud-gke.md)

#### 

## Step 3: Use the deployed services

If you don't have it installed already, install the [Postman client](https://www.getpostman.com) on your machine.

Then, download the Activiti Cloud Postman collection from the [Activiti Cloud Examples repository](https://github.com/Activiti/activiti-cloud-examples) using the command:

```bash
curl -o Activiti_v7_REST_API.postman_collection.json https://raw.githubusercontent.com/Activiti/activiti-cloud-examples/develop/Activiti%20v7%20REST%20API.postman_collection.json
```

Import the collection in Postman using _**Import**_ button.

Before calling any service you will need to create a new Environment in Postman. You can do that by going to the Manage Environment icon \(cog\)

![](../../.gitbook/assets/postman-manage-environment.png)

Then “Add” a new environment and add a name to it. Now you need to configure the variables for the environment: “gateway”, “idm” and “realm”

For gateway you need to copy the url associated with your Ingress, the same for idm which is SSO and IDM using Keycloak. For the realm enter “activiti”:

![](../../.gitbook/assets/postman-env-variables.png)

Click Save or Update and then you are ready to start using that Environment. Make sure that you select the environment in the dropdown on the right:

![](../../.gitbook/assets/postman-select-env-and-get-a-token.png)

As shown in the previous screenshot, if you go to the _**keycloak**_ directory and select the “_**getKeycloakToken testuser**_” you will get the token which will be used to authenticate further requests. Notice that this token is time sensitive and it will be automatically invalidated so you might need to get it again if you start getting unauthorized errors.

Once you get the token for a user, you can interact with all the user endpoints. For example, you can create a request to see which Process Definitions are deployed inside our Example Runtime Bundle:

![](../../.gitbook/assets/postman-get-processdef.png)

Now you can also start a new Process Instance from our SimpleProcess:

![](../../.gitbook/assets/postman-start-processinstance.png)

```bash
{
  "processDefinitionKey": "SimpleProcess",
  "variables": {
    "firstName": "salaboy",
    "lastName": "salaboy",
    "age": 99
  },
  "payloadType":"StartProcessPayload"
}
```

You can check that the audit service contains the events associated to the just started process instance.

![](../../.gitbook/assets/postman-get-audit-events.png)

And that the query service already contains information about the process execution:

![](../../.gitbook/assets/postman-query-processinstances.png)

You are now ready to start consuming these services to automate your own business processes.

Finally, you can access to all services Swagger documentation by pointing your browser to:

* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/rb-my-app/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/rb-my-app/swagger-ui.html) 
* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/audit/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/audit/swagger-ui.html) 
* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/query/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/query/swagger-ui.html)

![](../../.gitbook/assets/screenshot-2018-12-13-at-11.21.47.png)

All our services are using SpringFox to generate this documentation and provide a UI for it.

## Summary

In this tutorial we have seen how to create a Kubernetes cluster \(using GKE or EKS\) and deploy an Activiti Cloud application using the Activiti Cloud HELM charts. If you are not familiar with Kubernetes, Docker and GKE or AWS this might look like a lot of new information and it is our mission to simplify all the steps covered in these getting started guides. For that reason, we recommend you checkout the Jenkins X project, which greatly simplifies the first two sections about creating clusters and configuring the basic infrastructure for your projects.

As part of the Activiti Cloud initiative we are making sure that we follow best practices coming from the Kubernetes, Docker and Spring Cloud communities and we are contributing back with fixes and feedback to make this technology stack the best for Cloud Native applications.

If you have questions or feedback about this tutorial, feel free to get in touch with the Activiti team via the dedicated [Gitter](https://gitter.im/Activiti/Activiti7?utm_source=share-link&utm_medium=link&utm_campaign=share-link%20) [channel](https://gitter.im/Activiti/Activiti7?utm_source=share-link&utm_medium=link&utm_campaign=share-link%20).

