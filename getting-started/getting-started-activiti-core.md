# Getting Started - Activiti Core

## Getting Started with Activiti Core Runtime APIs

The new APIs were created with a clear purpose to address the following requirements:

* Provide a clear path to our Cloud approach
* Isolate internal and external APIs to provide backward compatibility moving forward
* Provide a future path to modularity by following the single responsibility approach
* Reduce the clutter of the previous version of APIs
* Include Security and Identity Management as first-class citizens
* Reduce time to value for common use cases, where you want to rely on the conventions provided by popular frameworks
* Provide alternative implementations of the underlying services
* Enable the community to innovate while respecting well-established contracts

We haven’t deprecated the old API, so you are still free to use it, but we strongly recommend the usage of the new API for long-term support.

This API is in a beta review, meaning that we might change and polish it before the GA release. We will appreciate all the feedback that we can get from our community users and if you want to get involved with the project please get in touch.

Time to get our hands dirty with a couple of example projects.

## TaskRuntime API

If you are building business applications, creating Tasks for users and groups in your organisation is something that you might find handy.

The TaskRuntime APIs are here to help you.

You can clone this example from GitHub: [https://github.com/Activiti/activiti-examples](https://github.com/Activiti/activiti-examples)

The code from this section can be found inside the “activiti-api-basic-task-example” maven module.

If you are running inside a Spring Boot 2 application you only need to add the activiti-spring-boot-starter dependency and a DB driver you can use H2 for an in-memory storage.

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/pom.xml\#L45](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/pom.xml#L45)

```text
<dependency>
    <groupId>org.activiti</groupId>
    <artifactId>activiti-spring-boot-starter</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

We recommend using our BOM \(bill of materials\)

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/pom.xml\#L30](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/pom.xml#L30)

```text
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-dependencies</artifactId>
            <version>7.0.0.XX</version>
            <scope>import</scope>
            <type>pom</type>
        </dependency>
    </dependencies>
</dependencyManagement>
```

{% hint style="info" %}
Note: make sure you replace "7.0.0.XX" with a valid Activiti version. Latest version can be retrieved using this link: [https://search.maven.org/search?q=activiti-dependencies](https://search.maven.org/search?q=activiti-dependencies)
{% endhint %}

Now let’s switch to our DemoApplication.class: [https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java\#L25](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java#L25)

Then you will be able to use the TaskRuntime

```text
@Autowired
private TaskRuntime taskRuntime;
```

Once you have the bean injected into your app you should be able to create tasks and interact with tasks.

```text
public interface TaskRuntime {
  TaskRuntimeConfiguration configuration();
  Task task(String taskId);
  Page tasks(Pageable pageable);
  Page tasks(Pageable pageable, GetTasksPayload payload);
  Task create(CreateTaskPayload payload);
  Task claim(ClaimTaskPayload payload);
  Task release(ReleaseTaskPayload payload);
  Task complete(CompleteTaskPayload payload);
  Task update(UpdateTaskPayload payload);
  Task delete(DeleteTaskPayload payload);
  ...
}
```

For example, you can create a task by doing:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java\#L45](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java#L45)

```text
taskRuntime.create(
            TaskPayloadBuilder.create()
                .withName("First Team Task")
                .withDescription("This is something really important")
                .withGroup("activitiTeam")
                .withPriority(10)
           .build());
```

This task will be only visible by users belonging to the activitiTeam and the owner \(the current logged in user\).

As you might have noticed, you can use TaskPayloadBuilder to parameterize the information that is going to be sent to the TaskRuntime in a fluent way.

In order to deal with Security, Roles and Groups we are relying on Spring Security modules. Because we are inside a Spring Boot application, we can use the UserDetailsService to configure the available users and their respective groups and roles. We are currently doing this inside a @Configuration class: [https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplicationConfiguration.java\#L26](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplicationConfiguration.java#L26)

Something important to notice here, is that in order to interact with the TaskRuntime API as a user, you need to have the role: ACTIVITI\_USER \(Granted Authority: ROLE\_ACTIVITI\_USER\) .

While interacting with REST endpoints the Authorization mechanism will set up the currently logged in user, but for the sake of the example we are using an utility class \([https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/SecurityUtil.java\#L26](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/SecurityUtil.java#L26) \) that allows us to set in the context a manually selected user. Notice that you should never do this unless you are trying things out and you want to change users without going through a REST endpoint. Look into the “web” examples to see more real scenarios where this utility class is not needed at all.

One last thing to highlight from the example is the registration of Task Event Listeners:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java\#L89](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-task-example/src/main/java/org/activiti/examples/DemoApplication.java#L89)

```text
@Bean
public TaskRuntimeEventListener taskAssignedListener() {
  return taskAssigned
           -> logger.info(
                 ">>> Task Assigned: '"
                + taskAssigned.getEntity().getName()
                +"' We can send a notification to the assignee: "
                + taskAssigned.getEntity().getAssignee());
}
```

You can register as many TaskRuntimeEventListeners as you want. This will enable your application to be notified when Runtime events are triggered by the services.

## ProcessRuntime API

In a similar fashion, if you want to start using the ProcessRuntime APIs, you need to include the same dependencies as before. We aim to provide more flexibility and separate runtimes in the future, but for now the same Spring Boot Starter is providing both TaskRuntime and ProcessRuntime API.

The code from this section can be found inside the “activiti-api-basic-process-example” maven module.

```text
public interface ProcessRuntime {
  ProcessRuntimeConfiguration configuration();
  ProcessDefinition processDefinition(String processDefinitionId);
  Page processDefinitions(Pageable pageable);
  Page processDefinitions(Pageable pageable,
              GetProcessDefinitionsPayload payload);
  ProcessInstance start(StartProcessPayload payload);
  Page processInstances(Pageable pageable);
  Page processInstances(Pageable pageable,
              GetProcessInstancesPayload payload);
  ProcessInstance processInstance(String processInstanceId);
  ProcessInstance suspend(SuspendProcessPayload payload);
  ProcessInstance resume(ResumeProcessPayload payload);
  ProcessInstance delete(DeleteProcessPayload payload);
  void signal(SignalPayload payload);
  ...
}
```

Similarly to the TaskRuntime APIs, in order to interact with the ProcessRuntime API the currently logged user is required to have the role “ACTIVITI\_USER”.

First things first, let’s autowire our ProcessRuntime:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java\#L32](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java#L32)

```text
@Autowired
private ProcessRuntime processRuntime;

@Autowired
private SecurityUtil securityUtil;
```

Same as before, we need our SecurityUtil helper to define in behalf of which user we are interacting with our APIs.

Now we can start interacting with the ProcessRuntime:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java\#L47](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java#L47)

```text
Page processDefinitionPage = processRuntime
                                .processDefinitions(Pageable.of(0, 10));
logger.info("> Available Process definitions: " +
                  processDefinitionPage.getTotalItems());
for (ProcessDefinition pd : processDefinitionPage.getContent()) {
  logger.info("\t > Process definition: " + pd);
}
```

Process Definitions needs to be placed inside the /src/main/resources/processes/ . For this example we have the following process defined:

We are using Spring Scheduling capabilities to start a process every second picking up random values from an array to process:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java\#L67](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java#L67)

```text
@Scheduled(initialDelay = 1000, fixedDelay = 1000)
public void processText() {
  securityUtil.logInAs("system");
  String content = pickRandomString();
  SimpleDateFormat formatter = new SimpleDateFormat("dd-MM-yy HH:mm:ss");
  logger.info("> Processing content: " + content
                    + " at " + formatter.format(new Date()));
  ProcessInstance processInstance = processRuntime
                  .start(ProcessPayloadBuilder
                       .start()
                       .withProcessDefinitionKey("categorizeProcess")
                       .withProcessInstanceName("Processing Content: " + content)
                       .withVariable("content", content)
                       .build());
  logger.info(">>> Created Process Instance: " + processInstance);
}
```

Same as before, we are using the ProcessPayloadBuilder to parameterize which process do we want to start and with which process variables in a fluent way.

Now if we look back at the Process Definition, you will find 3 Service Tasks. In order to provide the implementation for these service tasks you need to define Connectors:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java\#L81](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/java/org/activiti/examples/DemoApplication.java#L81)

```text
@Bean
public Connector processTextConnector() {
  return integrationContext -> {
      Map inBoundVariables = integrationContext.getInBoundVariables();
      String contentToProcess = (String) inBoundVariables.get("content")
     // Logic Here to decide if content is approved or not
     if (contentToProcess.contains("activiti")) {
        logger.info("> Approving content: " + contentToProcess);
        integrationContext.addOutBoundVariable("approved",true);
     } else {
        logger.info("> Discarding content: " + contentToProcess);
        integrationContext.addOutBoundVariable("approved",false);
     }
    return integrationContext;
  };
}
```

These connectors are wired up automatically to the ProcessRuntime using the Bean name, in this example “processTextConnector”. This bean name is picked up from the implementation property of the serviceTask element inside our process definition:

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/resources/processes/categorize-content.bpmn20.xml\#L22](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-process-example/src/main/resources/processes/categorize-content.bpmn20.xml#L22)

```text
<bpmn:serviceTask id="Task_1ylvdew" name="Process Content" implementation="processTextConnector">
```

This new Connector interface is the natural evolution of JavaDelegates, and the new version of Activiti Core will try to reuse your JavaDelagates by wrapping them up inside a Connector implementation:

```text
public interface Connector {
  IntegrationContext execute(IntegrationContext integrationContext);
}
```

Connectors receive an IntegrationContext with the process variables and return a modified IntegrationContext with the results that needs to be mapped back to process variables.

In the previous example, the connector implementation is receiving a “content” variable and adding an “approved” variables based on the content processing logic.

Inside these connectors you are likely to include System to System calls, such as REST calls and message based interactions. These interactions tends to become more and more complex and for such reason we will see in future tutorials how these connectors can be extracted from running outside of the context of the ProcessRuntime \(Cloud Connectors\), to decouple the responsibility of such external interactions outside of the ProcessRuntime scope.

Check the maven module activiti-api-spring-integration-example for a more advanced example using Spring Integrations to kickstart processes based on a File poller.

## Full Example

You can find an example using both, the ProcessRuntime and TaskRuntime APIs to automate the following process:

The code from this section can be found inside the “activiti-api-basic-full-example” maven module.

As the ProcessRuntime only example this is also categorizing some input content, but in this case, the process relies on a Human Actor to make the decision to approve the content or not. We have a schedule task as before that creates new process instances every 5 seconds and a simulated user checking if there are available tasks to work on.

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/java/org/activiti/examples/DemoApplication.java\#L63](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/java/org/activiti/examples/DemoApplication.java#L63)

And

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/java/org/activiti/examples/DemoApplication.java\#L85](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/java/org/activiti/examples/DemoApplication.java#L85)

The UserTask is created to a group of potentialOwners, in this example the “activitiTeam” group. But in this case, we are not creating the task manually as in the first example. The process instance is creating the task for us, every time that a process is started.

[https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/resources/processes/categorize-human-content.bpmn20.xml\#L38](https://github.com/Activiti/activiti-examples/blob/master/activiti-api-basic-full-example/src/main/resources/processes/categorize-human-content.bpmn20.xml#L38)

```text
<bpmn:userTask id="Task_1ylvdew" name="Process Content">
  <bpmn:incoming>SequenceFlow_09xowo4</bpmn:incoming>
  <bpmn:outgoing>SequenceFlow_1jzbgkj</bpmn:outgoing>
  <bpmn:potentialOwner>
    <bpmn:resourceAssignmentExpression>
      <bpmn:formalExpression>activitiTeam</bpmn:formalExpression>
    </bpmn:resourceAssignmentExpression>
  </bpmn:potentialOwner>
</bpmn:userTask>
```

Users belonging to this group will be able to claim and work on the task.

We encourage you to run these examples and experiment with them and get in touch if you have questions or if you find issues.

## Summary

In this tutorial, we have seen how to get started using the new ProcessRuntime and TaskRuntime API from the new Activiti Core Beta project.

We recommend you to check the Activiti Examples repository, for more examples: [https://github.com/Activiti/activiti-examples](https://github.com/Activiti/activiti-examples)

Helping us to write more of these examples might be a very good initial community contribution. Get in touch if you are interested, we are more than happy to guide you.

As always feel free to get in touch with us via Gitter: [https://gitter.im/Activiti/Activiti7?utm\_source=share-link&utm\_medium=link&utm\_campaign=share-link](https://gitter.im/Activiti/Activiti7?utm_source=share-link&utm_medium=link&utm_campaign=share-link) if you have questions or feedback about these examples and tutorials.

More blog posts are coming to introduce the Runtime Admin APIs and how these examples can be adapted to be executed in our new Activiti Cloud approach.

