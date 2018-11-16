## Docker Compose

If you choose the Docker Compose approach, you need to install Docker for your OS and Docker Compose will be ready to use in your terminal. \([https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)\)

Then you will need to clone the [github.com/activiti/activiti-cloud-examples](http://github.com/activiti/activiti-cloud-examples) repository or copy the contents of the docker/infrastructure-docker.yml file into your laptop and then run:

&gt; docker-compose -f infrastructure-docker.yml up -d

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-14-44-30.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-14-44-30/)

Before you can start interacting with your services you need to add an entry to your "/etc/hosts" file so the SSO \(Single Sign On\) component can sign the verification tokens using the same internal name as the services which are behind the gateway.

> sudo vi /etc/hosts  
> \# add for sso  
> 127.0.0.1       activiti-cloud-sso-idm

This will start up all the infrastructure services \(Gateway, Service Registry, Message Broker, Single Sign On / Identity Management \(Keycloak\)\)  so you can focus on creating your Activiti Cloud Applications.

Once you have all your infrastructure services started you can create a new Runtime Bundle \(which is going to be the brain of your Activiti Cloud Application\) Docker image with your business processes and required resources by using the project:

[https://github.com/Activiti/example-runtime-bundle](https://github.com/Activiti/example-runtime-bundle)

You can build a docker image for that project with `mvn clean install` and `docker build . -t activiti/example-runtime-bundle:latest`. Or you can skip this for now and use the [published image](https://hub.docker.com/r/activiti/example-runtime-bundle/) which is [already referenced in the docker-compose](https://github.com/Activiti/activiti-cloud-examples/blob/develop/docker/application-docker-compose.yml#L57)

You should be able to start your Activiti Cloud Application by using the docker/application-docker-compose.yml file:

> docker-compose -f application-docker-compose.yml up -d

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-07-59.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-07-59/)

This docker-compose file will start your runtime bundle plus the Query & Audit Service.

At this point you have the infrastructure and one Activiti Cloud Application ready to be used.

To shut everything down run:

> docker-compose -f application-docker-compose.yml down
>
> docker-compose -f infrastructure-docker.yml down

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-40-09.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-40-09/)
