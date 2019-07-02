# Amazon EKS

## Step 1: Create a Kubernetes cluster

### 1\) Install aws-iam-authenticator for Amazon EKS

Amazon EKS clusters requires the [AWS IAM Authenticator for Kubernetes](https://github.com/kubernetes-sigs/aws-iam-authenticator) to allow IAM authentication for your Kubernetes cluster. Use go get to install the aws-iam-authenticator binary:

```bash
go get -u -v github.com/kubernetes-sigs/aws-iam-authenticator/cmd/aws-iam-authenticator
```

{% hint style="info" %}
_Note: use Go 1.7 or greater._
{% endhint %}

Add $HOME/go/bin to your PATH environment variable:

* For Bash shells on macOS:

```bash
export PATH=$HOME/go/bin:$PATH && echo 'export PATH=$HOME/go/bin:$PATH' >> ~/.bash_profile
```

* For Bash shells on Linux:

```bash
 export PATH=$HOME/go/bin:$PATH && echo 'export PATH=$HOME/go/bin:$PATH' >> ~/.bashrc
```

Run this command to test that the aws-iam-authenticator binary works:

```bash
aws-iam-authenticator help
```

### **2/ Install AWS CLI**

To install the aws cli, check the user guide: [https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

Once installed, check your AWS CLI version with the following command:

```bash
aws --version
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
aws configure
AWS Access Key ID [None]: <your-access-key-ID>
AWS Secret Access Key [None]:<your-secet-access-key>
Default region name [None]: <your-region>
Default output format [None]: json
```

### 3/ Create an EKS cluster

To simplify the creation of our cluster on EKS, we are using a simple CLI tool named eksctl available here: [https://github.com/weaveworks/eksctl](https://github.com/weaveworks/eksctl).

To create a basic EKS cluster with a given name and region, run:

```bash
eksctl create cluster --name=<name> --region=<region>
```

A cluster will be created with default parameters:

* exciting auto-generated name, e.g. "fabulous-mushroom-1527688624"
* 2x `m5.large` nodes \(this instance type suits most common use-cases, and is good value for money\)
* use official AWS EKS AMI
* `us-west-2` region
* dedicated VPC \(check your quotas\)
* using static AMI resolver

Check the [eksctl doc](https://github.com/weaveworks/eksctl) if you want to change default parameters.

Go to your AWS console and check the details about your EKS cluster.

![AWS Console EKS dashboard.](https://lh4.googleusercontent.com/12kEk6ILvXG1RaVr-evgNwOeCzRAHaequODOj2a9iMC5Ahj53ZUrQvjbSl-Kx-sZLRXxbo0W1AsP9ES_VYwWAjgln0nIx7LYz5bjinmW-j-7BagbaTgKVYdRrcn9RGlpKtxp5jVH)

{% hint style="warning" %}
_Note: to delete your EKS cluster run the command:_

```bash
eksctl delete cluster --name=<name> --region=<region>
```

_Make sure it deleted all the associated EC2 resources avoiding you any bad surprises when checking your bill_ 😀
{% endhint %}

### **4\) Configure Kubectl for Amazon EKS**

Use the AWS CLI update-kubeconfig command to create or update your kubeconfig for your cluster.

```bash
aws eks update-kubeconfig --name <cluster_name>
```

Test your configuration:

```bash
kubectl get svc
```

## **Step 2: Configure HELM and install NGINX Ingress**

Let's now configure HELM to work in the Cluster. We first need to give HELM permissions to deploy things into the cluster. Download the file below:

{% file src="../../.gitbook/assets/helm-service-account-role.yaml" caption="helm-service-account-role.yaml" %}

Run the following commands in your terminal:

```bash
kubectl apply -f helm-service-account-role.yaml
```

```bash
helm init --service-account helm --upgrade
```

In order to be able to expose our services to be accessed from outside the cluster, we need to set up an Ingress Controller, which will automatically create routes to the internal services that we want to expose. To install the NGINX Ingress controller, run the following command:

```bash
helm install stable/nginx-ingress --version 1.5.0
```

Now that NGINX Ingress Controller is being deployed, we need to wait for it to expose itself using a Public IP. We need this Public IP to interact with our services from outside the cluster. You can find this IP by running the following command:

```bash
kubectl get services
```

Example output:

![External IP for NGINX Ingress controller.](../../.gitbook/assets/nginx-ingress-dns.png)

## [Step 3: Deploy Activiti Cloud Full Example](./#step-3-deploy-activiti-cloud-full-example)

