# Getting Started - Activiti Docker Compose

Follow this guide to setup Docker Compose to run Activiti Cloud on your local computer in Docker VM. 

## Hardware Setup

You will need to allocate at least 4 CPU cores and 8 Gb of RAM for your Docker VM machine. 

## Software Setup

Before you start, the following packages must be installed:

  * Docker for Desktop [Docker web site](https://www.docker.com/) or [Docker Toolbox](https://github.com/docker/toolbox/releases) for your OS 
  * Docker-compose [Docker-compose page](https://docs.docker.com/compose/install/) if you use Docker for Desktop. 
  * GNU Make. For Linux and Mac it is usually installed already, for Windows follow [Chocolatey GNU Make](https://chocolatey.org/packages/make) to install Make.
  * Git Bash Terminal . For Linux and Maс it is usually pre-installed. For Windows, follow [Chocolatey Git Install](https://chocolatey.org/packages/git.install) to install Git Bash Terminal.

## Clone Activiti Cloud Examples 

```sh
git clone https://github.com/Activiti/activiti-cloud-examples
cd docker-compose
```

## Configure Your Environment

You need to edit `.env` file to configure DOCKER_IP property based on your OS and Docker VM type. 

* Use your local computer IP address for Docker for Desktop on Linux, Mac or Windows
* Use `docker-machine ip` command if you use Docker Toolbox 

## How To Run Activity Cloud 

#### Start all services 

```sh
make all
```

#### Start all services without Modeler 

```sh
make application 
```

#### Start Modeler only

```sh
make modeler
```

#### Start all serices Using Docker Compose

```sh
docker-compose up
```

#### Stop All Services

```sh
make stop
```


#### Stop Application

```sh
make application/stop
```

#### Stop Modeler

```sh
make modeler/stop
```

#### Shutdown All Services

```sh
make down
```
#### Shutdown All Services Using Docker Compose
```sh
docker-compose down
```

### Usefull commands

To see and follow logs in your terminal

```sh
make logs 
```

To see logs for one service only

```sh
make  example-runtime-bundle/logs
```
Quickly ssh to service pod

```sh
make  example-runtime-bundle/ssh
```

Other available actions

```sh
make help
```

### Modeller 

To access modeler please open the url in your browser:

```sh
http://$DOCKER_IP/activiti-cloud-modeling
```

You will be redirected to keycloak where you have to use credentials *modeler/password* 

### Start sample process 

You will need to use [Postman](https://www.getpostman.com/downloads/) to use Activiti Rest Apis

Start Postman and add to Postman collection _Activiti v7 REST API.postman_collection.json_ located in https://github.com/Activiti/activiti-cloud-examples <br>

*Then at the top right choose manage environment then use your own ip in _current value_ tab.* <br> 
Then use _activiti_ as default environment. <br>
To start work execute _getKeycloakToken hruser_ in Postman Keycloak collection. Then run startProcess in rb-my-app Postman collection. 

### Common errors
* Please be sure that you are located in docker-compose folder before running commands.
* If something doesn't work with modeller first of all clear cookies or start in incognito window
* If you are using nip.io address make sure that local firewall or company firewall doesn't block requiests

