

## Getting Started

The first decision that you will need to make is to choose between running all services with:

* Docker Compose
* Kubernetes

The main difference between these two options is the degree of similarities that you will have a with a real production environment in your local setup. I would recommend Minikube if you are planning to run in Kubernetes hosted in a cloud provider. Minikube runs inside a VM, causing the application to be running inside a different instance of an Operating System. But if you're new to microservices Docker is a good place to start.

The Docker Compose approach is probably faster, as it doesn’t require a VM, but you will need to be careful with configurations. Remember that if you are planning to run your applications in a clustered environment, each of the services might run in a different node.  

No matter which option do you choose, from a high level perspective these are the steps that you will need to perform:

1. Start Infrastructure
2. New runtime bundle
   1. Configure it
   2. Build it
   3. Deploy it
   4. Repeat 2 for a new Runtime Bundle

## Docker Compose

If you choose the Docker Compose approach, you need to install Docker for your OS and Docker Compose will be ready to use in your terminal. \([https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)\)

Then you will need to clone the [github.com/activiti/activiti-cloud-examples](http://github.com/activiti/activiti-cloud-examples) repository or copy the contents of the docker/infrastructure.yml file into your laptop and then run:

&gt; docker-compose -f infrastructure-docker.yml up -d

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-14-44-30.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-14-44-30/)

Before you can start interacting with your services you need to add an entry to your "/etc/hosts" file so the SSO component can sign the verification tokens using the same internal name as the services which are behind the gateway. We have learnt a lot about Zuul \(Gateway\), Keycloak and how to do SSO with microservices, so expect a blog post about that shortly.

> sudo vi /etc/hosts  
> \# add for sso  
> 127.0.0.1       activiti-cloud-sso-idm

This will start up all the infrastructure services + Audit and Query so you can focus on creating your runtime bundles.

Once you have all your infrastructure services started you can create a new Runtime Bundle Docker image with your business processes and required resources by using the project:

[https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle)

Which doesn’t require any Java or Maven and it uses our Activiti-Cloud-Runtime-Bundle Docker image as base to just include your business processes included inside the /processes/ directory. In order to build your Runtime Bundle you just build it as any docker image, by running:

> docker build -t rb-my-app .

Inside the[docker-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle)directory, which contains the Dockerfile specification to build the image.

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-14-55-07.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-14-55-07/)

If you require more Java magic and customizations, you can use:

[https://github.com/Activiti/activiti-cloud-examples/tree/master/maven-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/maven-runtime-bundle)which is a simple project to create runtime bundle Docker Images by using the Fabric8 Docker Maven Plugin and our Activiti-Cloud-Runtime-Starter project. You can build the docker image by running:

> mvn clean install

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-00-21.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-00-21/)

The obvious advantage of using the Java/Maven approach is that you can include unit tests for your processes and customizations.

You need to make sure to tag your docker image accordingly \([https://github.com/Activiti/activiti-cloud-examples/blob/master/maven-runtime-bundle/pom.xml\#L100](https://github.com/Activiti/activiti-cloud-examples/blob/master/maven-runtime-bundle/pom.xml#L100)or when building the docker image manually with -t \) so then you can reference it in the rb-docker-compose.yml -&gt;[https://github.com/Activiti/activiti-cloud-examples/blob/master/docker/rb-docker-compose.yml\#L7](https://github.com/Activiti/activiti-cloud-examples/blob/master/docker/rb-docker-compose.yml#L7)

Once you have your Runtime Bundle Docker Image ready you should be able to start it with:

> docker-compose -f rb-docker-compose.yml up -d

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-07-59.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-07-59/)



At this point you have the infrastructure and one runtime bundle ready to be used.

To shut everything down run:

> docker-compose -f rb-docker-compose.yml down

> docker-compose -f infrastructure-docker.yml down

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-40-09.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-40-09/)

## MiniKube / Kubernetes

With Kubernetes we will achieve a more realistic environment to test our services. By using Minikube we will achieve a similar runtime environment as with docker compose, with the big difference that kubernetes will take care to self heal our services in the case of failures.

In order to get the minikube set up and running you need to first install minikube \([https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube)\) and start the cluster with:

> minikube start --memory 8000

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-43-57.png)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-43-57/)

You can change the memory allocation if you want to, Activiti Cloud with the current services is using around 3GB to bootstrap all the services.

You can access to the MiniKube dashboard running:

> minikube dashboard

It should look like this:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-11-43-59.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-11-43-59/)

And that will give you the MiniKube entry point IP address that you need to my to your /etc/hosts file

> sudo vi /etc/hosts  
> \# add for sso  
> 127.0.0.1       activiti-cloud-sso-idm  
> 192.168.64.4    activiti-cloud-sso-idm-kub

For my case the minikube IP address is: 192.168.64.4, but you should replace it for yours.

Once the cluster is ready you can start deploying services to it and you can do that by going to the /kubernetes/ directory inside the activiti-cloud-examples repository and run:

> kubectl create -f infrastructure.yml

Look at the Docker Compose section to read more about how to build your Runtime Bundle Docker images, and when you have those ready you can do:

> kubectl create -f runtime-bundle.yml

After running these commands you should see something like this in your Kubernetes Dashboard

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-11-51-53.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-11-51-53/)

Take a look at the runtime-bundle.yml \([https://github.com/Activiti/activiti-cloud-examples/blob/master/kubernetes/runtime-bundle.yml\#L17](https://github.com/Activiti/activiti-cloud-examples/blob/master/kubernetes/runtime-bundle.yml#L17)\) file for customizations regarding your Runtime Bundle image name and how to configure a Database for it. Notice that we are creating a Single Pod with both Runtime Bundle + PostgreSQL, but this is not a restriction, you can change your deployments to suit your needs.

A couple of caveats regarding this deployment:

* In minikube you can’t create service of type LoadBalancer, for that reason we are using NodePort. You will need to change this to LoadBalancer in two services: entrypoint and activiti-cloud-sso-idm-kub for a real Kubernetes environment. You will find comments explaining this in the infrastructure.yml deployments
* In minikube we need to use the NodePorts specified in the infrastructure.yml in order to interact with the Gateway and SSO services. In a real Kubernetes environment you will need to use the Service specified ports intestead.

Now that you have everything up and running, you need to know how to shut it down:

> kubectl delete -f runtime-bundle.yml

> kubectl delete -f infrastructure.yml

> minikube stop

# Is it working?

How do you test all these services? You have two options for now, but more are coming, you can use \(Chrome\) Postman Collection to hit the REST endpoints of these services and check that components such as Query and Audit are receiving events from our Process Executions or you can use our Demo Client App \(using Angular JS\) that will perform the security Web Flow, redirecting you to Keycloak UI for Single Sign On to all of our services. This simple application allow you to test the different endpoints to check that everything is working there.

## \(Chrome\) Postman Collection

Install Postman Chrome plugin, then download \(if you cloned the github repo for the cloud examples you already have this in your local environment\) the collection located here:[https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti%20v7%20REST%20API.postman\_collection.json](https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti%20v7%20REST%20API.postman_collection.json)

Import it into Postman and then you can execute different request against these services, mostly going through the API Gateway.

Depending on your environment setup you might need to change the IP addresses of the URLs in each request:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-00-28.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-00-28/)

Notice that to perform any request you first need to get a Token, which will be added to all the following requests that you make against the services. The token is set to expire after 3 minutes, meaning that you might need to request a new token if you wait for longer than that between interactions.

Once you have the token you can interact with any the other services that you have deployed.

Notice also that based on the name of your runtime bundle the URL for such request might change. The API Gateway will use the name of your Runtime Bundle App to register a new route to it when it is deployed, you will need to adapt that accordingly.

## Demo UI Client \(Angular JS\)

The other alternative, which looks more like building a client App, allows you to run an existing app that uses the Keycloak.js library to secure our rest calls and deal with requesting the token.

You can find this app in the demo-ui-client \([https://github.com/Activiti/activiti-cloud-examples/tree/master/demo-ui-client](https://github.com/Activiti/activiti-cloud-examples/tree/master/demo-ui-client)\) directory of the activiti-cloud-examples repository.

The README file in that directory guides you through the steps to execute the application.

Once you started the server you can access to:

> [http://localhost:3000/\#/main](http://localhost:3000/#/main)

In your browser and you should see something like this:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-07-45.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-07-45/)

This is Keycloak kicking in, asking you for your credentials. You can use **testuser/password** to login and proceed to the application:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-08-51.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-08-51/)

Again, here you will need to replace to your environment IP depending what setup are you testing. If you are running with Docker Compose \(default URL to localhost\) you can go ahead and execute some requests.

This should give you a high level idea about how to interact with these services and how to deploy new Runtime Bundles when you need them.





