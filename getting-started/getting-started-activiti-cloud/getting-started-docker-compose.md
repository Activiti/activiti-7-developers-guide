# Docker Compose

Follow this guide to setup Docker Compose to run Activiti Cloud on your local computer in Docker VM.

## Hardware Setup

You will need to allocate at least 4 CPU cores and 8 Gb of RAM for your Docker VM machine.

## Software Setup

Before you start, the following packages must be installed:

* Install [Docker for Desktop](https://www.docker.com/) or [Docker Toolbox](https://github.com/docker/toolbox/releases) for your OS 
* Install [Docker-compose](https://docs.docker.com/compose/install/) if you use Docker for Desktop. 
* Install GNU Make. For Linux and Mac it is usually installed already, for Windows use [Chocolatey GNU Make](https://chocolatey.org/packages/make) to install Make.
* Install Git Bash Terminal . For Linux and Maс it is usually pre-installed. If you use Docker for Desktop on Windows, use [Chocolatey Git Install](https://chocolatey.org/packages/git.install) to install Git Bash Terminal. 

## Clone Activiti Cloud Examples

Open Bash command line terminal and run these commands to clone [https://github.com/Activiti/activiti-cloud-examples](https://github.com/Activiti/activiti-cloud-examples) into your local environment:

```bash
git clone https://github.com/Activiti/activiti-cloud-examples
cd activiti-cloud-examples/docker-compose
```

## Configure Your Environment

You need to edit `.env` file to configure DOCKER\_IP property based on your OS and Docker VM type.

* Use your local computer IP address for Docker for Desktop on Linux, Mac or Windows
* Use `docker-machine ip` command if you use Docker Toolbox
* Don't use 127.0.0.1 or localhost

## How To Run Activity Cloud

#### Start Modeler

```bash
make modeler
```

After starting Modeler, wait for the containers to start. You can check the status by running `make ps` and `make logs` command to make sure that the containers are ready.

To access modeler please open the url in your browser:

```bash
http://$DOCKER_IP/activiti-cloud-modeling
```

You will be redirected to keycloak where you have to use credentials _modeler/password_

#### Start Application

```bash
make application
```

You will need to use [Postman](https://www.getpostman.com/downloads/) to use Activiti Rest Apis

Start Postman and add to Postman collection _Activiti v7 REST API.postman\_collection.json_ located in [https://github.com/Activiti/activiti-cloud-examples](https://github.com/Activiti/activiti-cloud-examples)   


_Then at the top right choose manage environment then use your own ip in current value tab._   
 Then use _activiti_ as default environment.   
 To start work execute _getKeycloakToken hruser_ in Postman Keycloak collection. Then run startProcess in rb-my-app Postman collection.

#### Start All Services

```bash
make all
```

#### Start all serices Using Docker Compose

```bash
docker-compose up
```

#### Stop All Services

```bash
make stop
```

#### Stop Application

```bash
make application/stop
```

#### Stop Modeler

```bash
make modeler/stop
```

#### Shutdown All Services

```bash
make down
```

#### Shutdown All Services Using Docker Compose

```bash
docker-compose down
```

### Usefull commands

To see and follow logs in your terminal

```bash
make logs
```

To see logs for one service only

```bash
make  example-runtime-bundle/logs
```

Quickly ssh to service pod

```bash
make  example-runtime-bundle/ssh
```

Other available actions

```bash
make help
```

### Common errors

* Please be sure that you are located in docker-compose folder before running commands.
* If something doesn't work with modeller first of all clear cookies or start in incognito window
* If you are using nip.io address make sure that local firewall or company firewall doesn't block requiests

