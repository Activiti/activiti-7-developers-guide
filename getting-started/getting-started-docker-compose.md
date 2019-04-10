# Getting Started - Activiti Docker Compose

Docker compose is the easiest way for running activiti cloud on computer. 
### Hardware 
At least 4 cores and 8 Gb RAM available in your Docker setup. 
## Additional packages
Before you start running Activiti in docker-compose following packages have to be installed:
  * Install Docker itself please find more details in [Docker web site] (https://www.docker.com/)
  * Install Docker-compose [https://docs.docker.com/compose/install/]
  * Install Make For linux and mac it is usually installed already, for windows  [http://gnuwin32.sourceforge.net/packages/make.htm] or if you are using chocolatey on windows ``choco install make``

## Prerequisite:
Change DOCKER_IP to your ip in .env

## Start options
You have several options for start.

#### Start all services
```
make all
```
or 
```
docker-compose up
```
#### Start all services without modeller 
```
make application 
```

#### Start modeler and keycloak
```
make modeler
```
### Usefull commands
To see logs 
```
make logs 
```
Other available actions
```
make help
```

### Modeller 
To access modeler please use url
```
http://$DOCKER_IP/activiti-cloud-modeling
```
For example
```
http://192.168.1.9/activiti-cloud-modeling
```
You will be redirected to keycloak where you have to use credentials *modeler/password* 

### Start sample process 
If Postman does not installed please download Postman https://www.getpostman.com/downloads/
Then 
```
git clone https://github.com/Activiti/activiti-cloud-examples
```
Then add to Postman collection _Activiti v7 REST API.postman_collection.json_ <br>
*Then at the top right choose manage environment then use your own ip in _current value_ tab.* <br> 
Then use _activiti_ as default environment. <br>
To start work execute _getKeycloakToken hruser_ in postman keycloak folder. <br>
Then run startProcess in rb-my-app postman folder. <br>


