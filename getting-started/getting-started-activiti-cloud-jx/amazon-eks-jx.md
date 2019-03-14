# Activiti Cloud Quickstart with Amazon EKS and Jenkins-X

## Setup Overview

The diagram below shows an EKS cluster deployed in AWS and exposed to the outside world using Network Load Balancer [NLB](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html) and Route 53 domain mapping. 

![](../../.gitbook/assets/eks-cluster.png)

Jenkins-X platform is then deployed and configured with Git provider to trigger Kubernetes-based pipelines which produce Docker images pushed into private Elastic Container Registry (ECR).  Jenkins-X will also deploy our Activiti Cloud Helm chart into an EKS cluster and expose it via ingress to the outside world. 

![](../../.gitbook/assets/activiti-kdl.png)

The diagram above uses Kubernetes Deployment Language: https://github.com/raffaelespazzoli/kdl

All of the persistence volume claims of Activiti Cloud infrastructure and applications will be handled by Amazon services using Amazon Elastic Block Store (Amazon EBS), which will be used to provision Kubernetes persistent volumes for Audit, Query, and Runtime Bundle using PostgreSQL database service.

## The Short Version

In case, you know what you are doing, get Jx from https://jenkins-x.io/getting-started/install/ and then follow the short version:

```
export AWS_ACCESS_KEY_ID=1234567890
export AWS_SECRET_ACCESS_KEY=123456789
export CLUSTER_NAME=activiti-cloud

$ jx upgrade cli

$ jx create cluster eks \
--cluster-name=$CLUSTER_NAME \
--skip-installation=true \
--node-type='m5.xlarge' \
--nodes=3 

$ jx install \
--provider=eks \
--no-default-environments

```

Then, skip to *Let’s Deploy Activiti Cloud in EKS* section at the end of this document.

## The Long version

### Configuring Domain Name using Nip.io

In case, we don’t need the real domain name, Jenkins-X provides support to use wildcard DNS nip.io service.  The nip.io allows to resolve any IP Address in the following DNS wildcard entries:

10.0.0.1.nip.io --> 10.0.0.1
app.10.0.0.1.nip.io --> 10.0.0.1
customer1.app.10.0.0.1.nip.io --> 10.0.0.1
customer2.app.10.0.0.1.nip.io --> 10.0.0.1
otherapp.10.0.0.1.nip.io --> 10.0.0.1

Simply put NIP.IO maps <anything>.<IP Address>.nip.io to the corresponding <IP Address>, simply by resolving 127.0.0.1.nip.io -> 127.0.0.1

To use nip.io DNS name, Jenkins-X will install and resolve the IP address of the Network Load Balancer during setup. Then the jenkins.1.2.3.4.nip,io DNS name would resolve to IP address, 1.2.3.4 giving wildcard DNS without need to configure DNS in Route 53.

## Configuring Domain Name in Route 53

To access our EKS cluster by domain name, we need to configure a real domain that will be used by users to access Activiti Cloud application and by DevOps team to access our Jenkins server.

In order to add a domain to AWS, choose Route 53 Service from the AWS console and click the blue Create Hosted Zone button. In the dialog box, type the name of your domain and click the blue Create button. 


![](../../.gitbook/assets/route53-hosted-zone.png)

After creating the hosted zone, you should see it in your Route 53 console below. You should configure your domain to delegate to the AWS name servers defined in NS record of your hosted zone as shown here:

![](../../.gitbook/assets/route53-record-set.png)

### Installing the Jenkins-X Cli

The Jenkins X provides Cli client called ‘jx’ (https://github.com/jenkins-x/jx). It is used to manage the Jenkins X infrastructure and Continuous Delivery pipelines. Detailed instructions on how to install jx, can be found https://jenkins-x.io/getting-started/install/

After successful installation of jx client you should now be able to display the jx client version by executing the following command:

```
$ jx version
NAME               VERSION
Jx                 1.3.836
```

#### Upgrade Warning for jx

jx version is aggressive about upgrading, so much so that the version command prompts to upgrade to the latest (if a newer is available). The default is Y , yes. To remove variables when using this GitOps approach, this author recommends saying n , no. Leave the upgrading of Jenkin-x to a purposeful plan both on the client and deployed infrastructure, ensuring both are always a match.

```
~ $ jx version
Failed to find helm installs: failed to run 'helm list' command in directory '', output: 'Error: Get https://localhost:6443/api/v1/namespaces/kube-system/pods?labelSelector=app%3Dhelm%2Cname%3Dtiller: dial tcp [::1]:6443: connect: connection refused': exit status 1
Failed to get kubernetes server version: Get https://localhost:6443/version?timeout=32s: dial tcp [::1]:6443: connect: connection refused
Failed to get kubectl version: Command failed 'kubectl version --short': Client Version: v1.13.2
The connection to the server localhost:6443 was refused - did you specify the right host or port? exit status 1

Failed to get helm version: failed to run 'helm version --short' command in directory '', output: 'Client: v2.12.2+g7d2b0c7
Error: Get https://localhost:6443/api/v1/namespaces/kube-system/pods?labelSelector=app%3Dhelm%2Cname%3Dtiller: dial tcp [::1]:6443: connect: connection refused': exit status 1
NAME VERSION
jx   1.3.126
git  git version 2.17.2 (Apple Git-113)

A new jx version is available: 1.3.769
? Would you like to upgrade to the new jx version? No

```