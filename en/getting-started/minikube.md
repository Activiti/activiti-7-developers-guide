## MiniKube / Kubernetes

With Kubernetes we will achieve a more realistic environment to test our services. By using Minikube we will achieve a similar runtime environment as with docker compose, with the big difference that kubernetes will take care to self heal our services in the case of failures, automatically kill and move our services to different nodes in the cluster when they miss behave, load balance our services and tons of other good things.

In order to get the minikube set up and running you need to first install minikube \([https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube)\) and start the cluster with:

> minikube start --memory 8000 --cpus 4

**note** I'm using

> minikube start --memory=8000 --cpus 4 --vm-driver xhyve

This require some extra installations, for more information about using xhyve driver, read the minikube installation guide.

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-43-57.png)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-43-57/)

You can change the memory allocation if you want to, Activiti Cloud with the current services is using around 3GB to bootstrap all the services in a clustered setup. That includes all the infrastructural services and one Activiti Cloud Application.

You can access to the MiniKube dashboard running:

> minikube dashboard

It should look like this:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-11-43-59.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-11-43-59/)

And that will give you the MiniKube entry point IP address that you need to my to your /etc/hosts file

> sudo vi /etc/hosts  
> \# add for sso  
> 127.0.0.1       activiti-cloud-sso-idm  
> 192.168.64.x    activiti-cloud-sso-idm-kub

For my case the minikube IP address is: 192.168.64.x, but you should replace it for yours. You will find the address of your minikube instance by running

> minikube dashboard

And looking into your browser URL.

**Note: Minikube runs inside a VM which means that it has a separate Docker Deamon. This means that if you build local docker images, those images will not be available to the Docker Deamon inside Minikube VM, for that reason it is recommened to point your Docker CLI tool to the minikube environment so images built locally in your environment are available to minikube. You can do this by running in your terminal**

> eval $(minikube docker-env)

It is important to understand that this is a valid configuration for the terminal where you executed this command, it is not a system wide configuration. So if you are build docker images you will need to do that in the same terminal.
The other alternative is, of course, to publish your docker images in a public registry such as Docker Hub or any other Docker registry that can be accessed by the minikube instance. Notice that you will face similar challanges if you are deploying to a real remote kubernetes cluster.

Once the cluster is ready you can start deploying services to it and you can do that by going to the /kubernetes/kubectl/ directory inside the activiti-cloud-examples repository and run:

> kubectl create -f infrastructure.yml

Look at the Docker Compose section to read more about how to build your Runtime Bundle Docker images, and when you have those ready you can do:

> kubectl create -f application.yml

After running these commands you should see something like this in your Kubernetes Dashboard

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-11-51-53.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-11-51-53/)

Take a look at the application.yml \([https://github.com/Activiti/activiti-cloud-examples/blob/master/kubernetes/kubectl/application.yml#L59](https://github.com/Activiti/activiti-cloud-examples/blob/master/kubernetes/kubectl/application.yml#L59)\) file for customizations regarding your Runtime Bundle image name and how to configure a Database for it. Notice that we are creating a Single Pod with both Runtime Bundle + PostgreSQL, but this is not a restriction, you can change your deployments to suit your needs.

A couple of caveats regarding this deployment:

* In minikube you can’t create service of type LoadBalancer, for that reason we are using NodePort. You will need to change this to LoadBalancer in two services: entrypoint and **activiti-cloud-sso-idm-kub** for a real Kubernetes environment. You will find comments explaining this in the infrastructure.yml deployments
* In minikube we need to use the NodePorts specified in the infrastructure.yml in order to interact with the Gateway and SSO services. In a real Kubernetes environment you will need to use the Service specified ports intestead.

Now that you have everything up and running, you need to know how to shut it down:

> kubectl delete -f application.yml
>
> kubectl delete -f infrastructure.yml
>
> minikube stop
