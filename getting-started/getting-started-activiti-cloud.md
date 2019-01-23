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

![Activiti Cloud application full example chart](../.gitbook/assets/activiti-cloud-full-example-chart.png)

One important thing to notice is that each of the Activiti Cloud components can be used independently. This example is intended to show a large-scale deployment scenario. You can start small with a Runtime Bundle \(which provides the process and task runtimes\), but if you want to scale things up you need to know what you are aiming for, and this charts shows you exactly that.

### Install Kubectl, HELM and Activiti Cloud Full Example

* Kubectl : [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
* HELM: [https://docs.helm.sh/using\_helm/\#installing-helm](https://docs.helm.sh/using_helm/#installing-helm)

Clone the [https://github.com/Activiti/activiti-cloud-charts](https://github.com/Activiti/activiti-cloud-charts) and go to the “activiti-cloud-full-example” directory, we will use some files from there.

```bash
$ git clone https://github.com/Activiti/activiti-cloud-charts
$ cd activiti-cloud-charts/activiti-cloud-full-example
```

The next consist in creating a Kubernetes cluster that you will use to deploy the Activiti Cloud full example. Here are the instructions for:

* Google Cloud Platform  - GKE \(free option\)
* Amazon Web Services - EKS

We let you decide which cloud platform best suits you. You can also deploy the Activiti Cloud full example on you local machine using for example Docker Desktop. We recommend using a cloud infrastructure for a faster and smoother experience but if you need a local install you can check [our blog post series here](https://community.alfresco.com/community/bpm/blog/2018/12/10/getting-started-with-activiti-7-beta#jive_content_id_Deploying_and_Running_a_Business_Process). 

## Step 1: Create a Kubernetes cluster

### A\) Using Google Cloud Platform - GKE

#### 1\) Create a Google Cloud Platform \(GCP\) account

As a free option, GCP offers a $300 free credit: [https://console.cloud.google.com/freetrial](https://console.cloud.google.com/freetrial)

####  2\) Install CLI

Install the SDK CLI tool: [https://cloud.google.com/sdk/install](https://cloud.google.com/sdk/install)

#### 3\) Create a Kubernetes cluster \(GKE\)

To create a new Kubernetes cluster, go to your GCP Console Home Page \([https://console.cloud.google.com](https://console.cloud.google.com/)\) and select _**Kubernetes Engine / Clusters.**_

![](../.gitbook/assets/gcp-console.png)

Then select the CREATE CLUSTER button ![](../.gitbook/assets/create-gke-cluster-button.png) from the top menu.

Enter the Cluster Name, select the Zone based on your location and I’ve selected 2 vCPUs and left the Size to the default value \(3\).

![GKE cluster creation settings](../.gitbook/assets/create-cluster-parameters.png)

Once the cluster is created click on the Connect Button on the right hand side of the table:

![Kubernetes clusters list view](../.gitbook/assets/cluster-connect.png)

This will open a popup with a command to connect to the cluster. Copy it to your clipboard, open a terminal and paste the command into your terminal as shown below.

![](../.gitbook/assets/screenshot-2018-12-13-at-10.13.17.png)

Now you have your cluster configured and ready to be used.

{% hint style="info" %}
_Note: if you are working with an existing cluster, you will need to check if you have an Ingress Controller already installed, you can skip the following steps if that is the case._
{% endhint %}

### B\) Using Amazon Web Services - EKS

#### 1\) Install aws-iam-authenticator for Amazon EKS

Amazon EKS clusters requires the [AWS IAM Authenticator for Kubernetes](https://github.com/kubernetes-sigs/aws-iam-authenticator) to allow IAM authentication for your Kubernetes cluster. Use go get to install the aws-iam-authenticator binary:

```bash
$ go get -u -v github.com/kubernetes-sigs/aws-iam-authenticator/cmd/aws-iam-authenticator
```

{% hint style="info" %}
_Note: use Go 1.7 or greater._
{% endhint %}

Add $HOME/go/bin to your PATH environment variable:

* For Bash shells on macOS:

```bash
$ export PATH=$HOME/go/bin:$PATH && echo 'export PATH=$HOME/go/bin:$PATH' >> ~/.bash_profile
```

* For Bash shells on Linux:

```bash
 $ export PATH=$HOME/go/bin:$PATH && echo 'export PATH=$HOME/go/bin:$PATH' >> ~/.bashrc
```

Run this command to test that the aws-iam-authenticator binary works:

```bash
$ aws-iam-authenticator help
```

#### **2/ Install AWS CLI**

To install the aws cli, check the user guide: [https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

Once installed, check your AWS CLI version with the following command:

```bash
$ aws --version
```

Example output:

```text
aws-cli/1.16.87 Python/3.7.2 Darwin/18.2.0 botocore/1.12.77
```

{% hint style="info" %}
_Note: your system's Python version must be Python 3, or Python 2.7.9 or greater. Otherwise, you receive hostname doesn't match errors with AWS CLI calls to Amazon EKS._
{% endhint %}

Configure your AWS CLI to interact with your AWS account using the command below:

```text
$ aws configure
AWS Access Key ID [None]: <your-access-key-ID>
AWS Secret Access Key [None]:<your-secet-access-key>
Default region name [None]: <your-region>
Default output format [None]: json
```

#### 3/ Create a Kubernetes cluster \(EKS\)

To simplify the creation of our cluster on EKS, we are using a simple CLI tool named eksctl available here: [https://github.com/weaveworks/eksctl](https://github.com/weaveworks/eksctl).

To create a basic EKS cluster with a given name and region, run:

```bash
$ eksctl create cluster [--name=<name>] [--region=<region>]
```

Go to your AWS console and check the details about your EKS cluster.

![AWS Console EKS dashboard.](https://lh4.googleusercontent.com/12kEk6ILvXG1RaVr-evgNwOeCzRAHaequODOj2a9iMC5Ahj53ZUrQvjbSl-Kx-sZLRXxbo0W1AsP9ES_VYwWAjgln0nIx7LYz5bjinmW-j-7BagbaTgKVYdRrcn9RGlpKtxp5jVH)

#### **4\) Configure Kubectl for Amazon EKS**

Use the AWS CLI update-kubeconfig command to create or update your kubeconfig for your cluster.

```bash
$ aws eks update-kubeconfig --name <cluster_name>
```

Test your configuration:

```bash
$ kubectl get svc
```

##  **Step 2: Configure HELM and install NGINX Ingress**

Let's now configure HELM to work in the Cluster. We first need to give HELM permissions to deploy things into the cluster. From the “activiti-cloud-full-example” directory, run the commands below in your terminal:

```bash
$ kubectl apply -f helm-service-account-role.yaml
```

```bash
$ helm init --service-account helm --upgrade
```

Expected results:

![](../.gitbook/assets/screenshot-2018-12-13-at-10.40.41.png)

![](../.gitbook/assets/screenshot-2018-12-13-at-10.42.56.png)

One more thing that we need to do in order to be able to expose our services to be accessed from outside the cluster is to set up an Ingress Controller, which will automatically create routes to the internal services that we want to expose, in order to do this we just need to run the following command:

```bash
$ helm install stable/nginx-ingress
```

Expected results:

![](../.gitbook/assets/helm-install-nginx.png)

Now that NGINX Ingress Controller is being deployed, we need to wait for it to expose itself using a Public IP. We need this Public IP to interact with our services from outside the cluster. You can find this IP by running the following command:

```bash
$ kubectl get services
```

Example output with GCP deployment:

![](../.gitbook/assets/kubectl-get-services-ip.png)

We will use nip.io as DNS service to map our services to this External IP which will follow the following format: ..nip.io

Example output with AWS deployment:

![](../.gitbook/assets/nginx-ingress-dns.png)

## Step 3: Deploy Activiti Cloud Full Example

Now that we have our Cluster in place, HELM installed and an Ingress Controller to access our services from outside the cluster we are ready to deploy the Activiti Cloud Full Example HELM Chart.

The first step is to register the Activiti Cloud HELM charts into HELM. We do this by running the following commands:

```bash
$ helm repo add activiti-cloud-charts https://activiti.github.io/activiti-cloud-charts/
```

```bash
$ helm repo update
```

Example output:

![](../.gitbook/assets/screenshot-2018-12-13-at-10.55.52.png)

The next step is to configure your deployment to your cluster. The Activiti Cloud Full Example Chart can be customized to turn on and off different features, but there is one mandatory parameter that needs to be provided which is the external domain name that is going to be used by this installation.

### A\) Configure your deployment for GCP

Open the “_values.yaml_” file located at: activiti-cloud-charts/activiti-cloud-full-example.

Set the Activiti Cloud modeling to true and replace the 3 occurrences of the string “**REPLACEME**” to "**&lt;EXTERNAL-IP&gt;.nip.io**". In our case: **104.155.53.158.nip.io** in every occurrence of “**REPLACEME**”.

Example "_values.yaml_" file:

```yaml
# Default values for activiti-cloud-full-example.
...

global:
  keycloak:
    url: "http://activiti-keycloak.104.155.60.221.nip.io/auth"
  gateway:
    host: &gatewayhost "activiti-cloud-gateway.104.155.60.221.nip.io"

activiti-cloud-modeling:
  enabled: true
...
infrastructure:
  activiti-keycloak:
    keycloak:
      enabled: true
      keycloak:
        ingress:
          enabled: true
          path: /
          proxyBufferSize: "16k"
          hosts:
            - "activiti-keycloak.104.155.60.221.nip.io"
 ...
```

### B\) Configure your deployment for AWS

With AWS, you need to create a new Record Set in Route 53. To do so, go to the AWS Management Console and open the Route 53 console. Select a public Hosted Zones and create a new Record Set. Name it using “\*” character in order to create a wildcard. In the Alias Target, select the DNS name of the Ingress controller that we deployed earlier. Use the following command to get the ELB DNS name:

```bash
$ kubectl get services
```

![New Record Set in Route 53.](../.gitbook/assets/route-53-record-set-elb-dns.png)

Open the “_values.yaml_” file located at: activiti-cloud-charts/activiti-cloud-full-example.

Set the Activiti Cloud modeling to true and replace the 3 occurrences of the string “**REPLACEME**” to "**&lt;your-public-domain&gt;**". In our case: _**raphaelallegre.com**_ in every occurrence of “**REPLACEME**”.

Example "_values.yaml_" file:

```yaml
# Default values for activiti-cloud-full-example.
...

global:
  keycloak:
    url: "http://activiti-keycloak.raphaelallegre.com/auth"
  gateway:
    host: &gatewayhost "activiti-cloud-gateway.raphaelallegre.com"

activiti-cloud-modeling:
  enabled: true
...
infrastructure:
  activiti-keycloak:
    keycloak:
      enabled: true
      keycloak:
        ingress:
          enabled: true
          path: /
          proxyBufferSize: "16k"
          hosts:
            - "activiti-keycloak.raphaelallegre.com"
 ...
```

### C\) Deploy the Helm chart

Once you have performed the 4 changes, deploy the chart by running the following command:

```bash
$ helm install -f values.yaml activiti-cloud-charts/activiti-cloud-full-example
```

Expected results:

![](../.gitbook/assets/deploy-activiti-cloud.png)

This will trigger the deployment process, and you need to wait until the services are up and running. You can check this by running:

```bash
kubectl get pods
```

Expected results:

![](../.gitbook/assets/pods-services-up.png)

{% hint style="info" %}
_Note: the READY column 1/1 in all the pods, that means that we have 1 pod in Kubernetes running our service. It is also important to notice that HELM created a release of our CHART. Because we haven’t specified a name for this release HELM choose one random name, in our case, it was: bumptious-yak. This means that we can manage this release independently of other Activiti Cloud Applications that we want to deploy using the same approach. You can run helm list and then helm delete to remove all the Activiti Cloud Services for this release._
{% endhint %}

In order to access to your services now, you can run the following command:

```bash
kubectl get ingress
```

Expected results with GCP deployment:

![](../.gitbook/assets/kubectl-get-ingress.png)

Expected results with AWS deployment:

![](../.gitbook/assets/kube-get-ingress-aws-eks.png)

### D\) Access your services

You can access the Keycloak admin console and the Activiti Cloud Modeling application at:

For GCP at:

* [http://activiti-keycloak](http://activiti-keycloak).&lt;EXTERNAL-IP&gt;.nip.io/auth \(admin/admin\)
* [http://activiti-cloud-gateway](http://activiti-cloud-gateway).&lt;EXTERNAL-IP&gt;.nip.io/activiti-cloud-modeling \(modeler/password\)

For AWS at:

* [http://activiti-keycloak](http://activiti-keycloak).&lt;yourpublicdomain&gt;/auth \(admin/admin\)
* [http://activiti-cloud-gateway](http://activiti-cloud-gateway).&lt;yourpublicdomain&gt;/activiti-cloud-modeling \(modeler/password\)

In our case, here is the BPMN 2 process modelling application:

![Activiti BPMN 2 process modelling application.](../.gitbook/assets/activiti-modeling-application.png)

For more information about the BPMN modelling application, please check the [following blog post](https://community.alfresco.com/community/bpm/blog/2018/12/10/activiti-7-beta-using-the-modeler-to-design-business-processes).

## Step 4: Interact with your Application

If you don't have it installed already, install the [Postman client](https://www.getpostman.com) on your machine.

Then, download the Activiti Cloud Postman collection from the [Activiti Cloud Examples repository](https://github.com/Activiti/activiti-cloud-examples) using the command:

```bash
curl -o Activiti_v7_REST_API.postman_collection.json https://raw.githubusercontent.com/Activiti/activiti-cloud-examples/develop/Activiti%20v7%20REST%20API.postman_collection.json
```

Import the collection in Postman using _**Import**_ button.

Before calling any service you will need to create a new Environment in Postman. You can do that by going to the Manage Environment icon \(cog\)

![](../.gitbook/assets/postman-manage-environment.png)

Then “Add” a new environment and add a name to it. Now you need to configure the variables for the environment: “gateway”, “idm” and “realm”

For gateway you need to copy the url associated with your Ingress, the same for idm which is SSO and IDM using Keycloak. For the realm enter “activiti”:

![](../.gitbook/assets/postman-env-variables.png)

Click Save or Update and then you are ready to start using that Environment. Make sure that you select the environment in the dropdown of the right:

![](../.gitbook/assets/postman-select-env-and-get-a-token.png)

As shown in the previous screenshot, if you go to the _**keycloak**_ directory and select the “_**getKeycloakToken testuser**_” you will get the token which will be used to authenticate further requests. Notice that this token is time sensitive and it will be automatically invalidated so you might need to get it again if you start getting unauthorized errors.

Once you get the token for a user, you can interact with all the user endpoints. For example, you can create a request to see which Process Definitions are deployed inside our Example Runtime Bundle:

![](../.gitbook/assets/postman-get-processdef.png)

Now you can also start a new Process Instance from our SimpleProcess:

![](../.gitbook/assets/postman-start-processinstance.png)

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

![](../.gitbook/assets/postman-get-audit-events.png)

And that the query service already contains information about the process execution:

![](../.gitbook/assets/postman-query-processinstances.png)

You are now ready to start consuming these services to automate your own business processes.

Finally, you can access to all services Swagger documentation by pointing your browser to:

* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/rb-my-app/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/rb-my-app/swagger-ui.html) 
* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/audit/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/audit/swagger-ui.html) 
* [http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/query/swagger-ui.html](http://activiti-cloud-gateway.EXTERNAL-IP.nip.io/query/swagger-ui.html)

![](../.gitbook/assets/screenshot-2018-12-13-at-11.21.47.png)

All our services are using SpringFox to generate this documentation and provide a UI for it.

## Summary

In this quick-start we have seen how to create a Kubernetes cluster \(using GKE\) and deploy an Activiti Cloud application using the Activiti Cloud HELM charts. If you are not familiar with Kubernetes, Docker and GKE this might look like a lot of new information and it is our mission to simplify all the steps covered in these getting started guide. For that reason, we recommend you to checkout the Jenkins X project, which greatly simplifies the first two sections about creating clusters and configuring the basic infrastructure for your projects.

As part of the Activiti Cloud initiative we are making sure that we follow best practices coming from the Kubernetes, Docker and Spring Cloud communities and we are contributing back with fixes and feedback to make this technology stack the best for Cloud Native applications.

If you have questions or feedback about this tutorial, feel free to get in touch with the Activiti team via the dedicated [Gitter](https://gitter.im/Activiti/Activiti7?utm_source=share-link&utm_medium=link&utm_campaign=share-link%20) [channel](https://gitter.im/Activiti/Activiti7?utm_source=share-link&utm_medium=link&utm_campaign=share-link%20).

