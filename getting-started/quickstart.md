(Logo)
(Title)

This Quickstart is aimed to let you get up and running with Activiti Cloud BPM in almost no time. Due to its ultra scalable and modular design, there are several components to download and start. We want to hide this complexity until you are ready for deployment. For testing and proof of concept purposes, this quickstart is more than enough to encourage you to try your own BPMN processes inside Activiti.
For this Quickstart guide to work, you need to have [Docker](http;//www.docker.com) and [Git](http://git.com) installed in your machine. If not, please begin by downloading and setting it up in your environment.

Once you have Docker up and running, follow the next steps

git clone https://github.com/Activiti/activiti-cloud-examples
cd activiti-cloud-examples
echo '127.0.0.1 activiti-cloud-sso-idm' | sudo tee -a /etc/hosts
cd docker
docker-compose -f infrastructure-docker.yml up -d
docker-compose -f application-docker-compose.yml up -d

Then point your browser to http://localhost:3000.

You will be prompted an username and password. Login with testuser/password. By clicking Login you will be redirected to our Demo UI.
(Screenshot)

Within this interface you can interact with the BPMN engine. We have provided some sample BPMN definition for you to play with. You can see them by
clicking the QUERY PROCESS DEFINITIONS button.

(Screenshot)

the first thing to do in a BPM environment is to start a process,

(Screenshot)

 do this by clicking the "Start Process" button
 Screenshot del bpmn process con bpmn.io

 explicar que pasa detras de bambalinas cuando inicias el proceso. Claim, Complete.

Dispite the complexity of the previous steps you have now have the possibility to write infinitely scalable processes, up to glocal scale
This is the power of Activiti Cloud !

For more extensive and comprehensive documentation please refer to the [Getting Started Guide](./getting-started/getting-started.md)

Hacer review document bpmno.
