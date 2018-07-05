# Activiti Cloud Quickstart Guide

[![Activiti](/assets/Acitiviti_Icon_FullColor_GitHub_400x400.png)](https://github.com/Activiti)

Welcome to the new Activiti Cloud project. This Quickstart is aimed to let you get up and running with Activiti Cloud BPM in almost no time.

Due to its ultra scalable and modular design, there are several components to download and start. We want to hide this complexity until you are ready for deployment. For testing and proof of concept purposes, this quickstart is more than enough to encourage you to try your own BPMN processes inside Activiti.  
For this Quickstart guide to work, you need to have [Docker](http;//www.docker.com) and [Git](http://git.com) installed in your machine. If not, please begin by downloading and setting it up in your environment.

Once you have Docker up and running, follow the next steps

`git clone https://github.com/Activiti/activiti-cloud-examples`  
`cd activiti-cloud-examples`  
`echo '127.0.0.1 activiti-cloud-sso-idm' | sudo tee -a /etc/hosts`  
`cd docker`  
`docker-compose -f infrastructure-docker-compose.yml up -d`  
`docker-compose -f application-docker-compose.yml up -d`

Then point your browser to [http://localhost:8080](http://localhost:8080).

![](/assets/keycloak.png)

You will be prompted an username and password. Login with testuser/password. By clicking Login you will be redirected to the Demo UI.

![](/assets/demo-ui-intro.png)

Within this interface you can interact with the BPMN engine. We have provided some sample BPMN definitions for you to play with. You can see them by clicking the "Query Process Definition" button.

![](/assets/query-process-definitions.png)

Take note of the uniquely generated ID. It is needed to start an instance of the process. Now with this UUID, we can paste it into the "processDefinitionId" JSON property of the REST message. The click on the "Start Process" button

![](/assets/started-process.png)

This will generate a new instance of the process of the following BPMN process definition:

![](/assets/simple-process.png)

As you can see it is very simple, mostly like a "Hello, World". We can query the process instances currently running in our engine instance by clicking on the "Query Process Instances" button. This will print the following output:

![](/assets/query-process-instances.png)

Once we have a process instance running, it is time to see the available tasks for the user. In this case, we only have one possible task to execute, as you can see in the output of the "Query Tasks" button:

![](/assets/query-processes-tasks.png)

Following the lines of the process instances, Tasks also have an unique identifier. Take note of the Id JSON attribute value, as we will need it for the following step, Claiming the task as ours to complete:

![](/assets/claimed-task.png)

Be sure to paste the Task Id in the {taskId} placeholder in the query string. After we have claimed the task, we can complete it. Of course, this is a simplification of the real world, where we might expect to have different both manual and automatic tasks to execute, in both serial and parallel ways, increasing the complexity of the end user UI and the process definition.

To complete the task, we must again replace the placeholder for the taskId with our real Task Identifier, and then click the "Complete Task" button:

![](/assets/complete-task.png)

That's it, we have successfully finished the process instance. Finally in case we want to see the trace of all the tasks and process activity we can use the "Query Audit Events" button:

![](/assets/audit-events.png)

This will render a complete list of all the engine activity. It is very useful to have a very through trace for both process debugging and end user auditing.

## Next Steps

Despite the complexity of the previous steps you now have the possibility to write infinitely scalable processes, up to global scale  
This is the power of Activiti 7 and Activiti Cloud!

For more extensive and comprehensive documentation please refer to the [Getting Started Guide](./getting-started/getting-started.md)

