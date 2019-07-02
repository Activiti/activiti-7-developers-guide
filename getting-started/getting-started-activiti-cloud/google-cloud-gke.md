# Google Cloud GKE

## Step 1: Create a Kubernetes cluster

### 1\) Create a Google Cloud Platform \(GCP\) account

As a free option, GCP offers a $300 free credit: [https://console.cloud.google.com/freetrial](https://console.cloud.google.com/freetrial)

### 2\) Install CLI

Install the SDK CLI tool: [https://cloud.google.com/sdk/install](https://cloud.google.com/sdk/install)

### 3\) Create a Kubernetes cluster \(GKE\)

To create a new Kubernetes cluster, go to your GCP Console Home Page \([https://console.cloud.google.com](https://console.cloud.google.com/)\) and select _**Kubernetes Engine / Clusters.**_

![](../../.gitbook/assets/gcp-console.png)

Then select the CREATE CLUSTER button ![](../../.gitbook/assets/create-gke-cluster-button.png) from the top menu.

Enter the Cluster Name, select the Zone based on your location and I’ve selected 2 vCPUs and left the Size to the default value \(3\).

![GKE cluster creation settings](../../.gitbook/assets/create-cluster-parameters.png)

Once the cluster is created click on the Connect Button on the right hand side of the table:

![Kubernetes clusters list view](../../.gitbook/assets/cluster-connect.png)

This will open a popup with a command to connect to the cluster. Copy it to your clipboard, open a terminal and paste the command into your terminal.

{% hint style="info" %}
_Note: if you are working with an existing cluster, you will need to check if you have an Ingress Controller already installed, you can skip the NGINX Ingress installation \(step 2\) if that is the case._
{% endhint %}

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

Example output with GCP deployment:

![External IP for NGINX Ingress controller.](../../.gitbook/assets/kubectl-get-services-external-ip.png)

## [Step 3: Deploy Activiti Cloud Full Example](./#step-3-deploy-activiti-cloud-full-example)

