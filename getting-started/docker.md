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

[https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle)

Which doesn’t require any Java or Maven and it uses our Activiti-Cloud-Runtime-Bundle Docker image as base to just include your business processes included inside the /processes/ directory. In order to build your Runtime Bundle you just build it as any docker image, by running:

> docker build -t rb-my-app .

Inside the [docker-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/docker-runtime-bundle) directory, which contains the Dockerfile specification to build the image.

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-14-55-07.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-14-55-07/)

If you require more Java magic and customizations, you can use:

[https://github.com/Activiti/activiti-cloud-examples/tree/master/maven-runtime-bundle](https://github.com/Activiti/activiti-cloud-examples/tree/master/maven-runtime-bundle)which is a simple project to create runtime bundle Docker Images by using the Fabric8 Docker Maven Plugin and our Activiti-Cloud-Runtime-Starter project. You can build the docker image by running:

> mvn clean install

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-00-21.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-00-21/)

The obvious advantage of using the Java/Maven approach is that you can include unit tests for your processes and customizations.

You need to make sure to tag your docker image accordingly \([https://github.com/Activiti/activiti-cloud-examples/blob/master/maven-runtime-bundle/pom.xml\#L100](https://github.com/Activiti/activiti-cloud-examples/blob/master/maven-runtime-bundle/pom.xml#L100) or when building the docker image manually with -t \) so then you can reference it in the application-docker-compose.yml -&gt;[https://github.com/Activiti/activiti-cloud-examples/blob/master/docker/application-docker-compose.yml#L40](https://github.com/Activiti/activiti-cloud-examples/blob/master/docker/application-docker-compose.yml#L40)

Once you have your Runtime Bundle Docker Image ready you should be able to start your Activiti Cloud Application by using the docker/application-docker-compose.yml file:

> docker-compose -f application-docker-compose.yml up -d

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-07-59.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-07-59/)

This docker-compose file will start your runtime bundle plus the Query & Audit Service.

At this point you have the infrastructure and one Activiti Cloud Application ready to be used.

To shut everything down run:

> docker-compose -f application-docker-compose.yml down
>
> docker-compose -f infrastructure-docker.yml down

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-09-at-15-40-09.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-09-at-15-40-09/)
