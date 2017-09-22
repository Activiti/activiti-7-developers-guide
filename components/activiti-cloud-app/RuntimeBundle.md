# Runtime Bundle

Runtime Bundle is the Cloud version of the Process Engine.
If you ever exposed Activiti (the process engine) as a service, you were using a Runtime Bundle.
But there are a couple of extra things that you need to know:
- Runtime Bundles in the context of Activiti Cloud represent a stateless instance of the process engine which is in charge of executing an immutable set of process definitions.
- You cannot deploy new process definitions to a Runtime Bundle, instead you will create a new immutable Runtime Bundle if you  want to update your process definitions.
- Runtime Bundles expose a (Sync) REST and (Async) Message Based API to interact with them.
- Runtime Bundles emit events in a fire & forget fashion using a default EventListener (Listen to the internal Process Engine events and transform them into messages)

![](../../../assets/RuntimeBundle.png)

## REST APIs (HAL)
Runtime Bundles expose a REST API with the following endpoints:
- /vX/process-definitions/
- /vX/process-instances/
- /vX/tasks

## (Async) Command Based Interactions
Commands are defined inside the *activiti-services-core-model* project
and they represent different actions that can be executed by the process engine and they will return a Command Result. When these commands are executed by the Message Endpoints the results will be sent to a different queue.

- *Commands* (implements [Command](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-api/src/main/java/org/activiti/services/api/commands/Command.java) and extends [AbstractCommand](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/AbstractCommand.java))
  - [StartProcessInstanceCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/StartProcessInstanceCmd.java)
    - processDefinitionId
    - variables
  - [SuspendProcessInstanceCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/SuspendProcessInstanceCmd.java)
    - processDefinitionId
  - [ActivateProcessInstanceCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/ActivateProcessInstanceCmd.java)
    - processDefinitionId
  - [SignalProcessInstancesCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/SignalProcessInstancesCmd.java)
    - name
    - inputVariables
  - [ClaimTaskCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/ClaimTaskCmd.java)
  - [ReleaseTaskCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/ReleaseTaskCmd.java)
  - [CompleteTaskCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/CompleteTaskCmd.java)
    - inputVariables
  - [SetTaskVariableCmd](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-core-model/src/main/java/org/activiti/services/core/model/commands/SetTaskVariablesCmd.java)
    - taskId
    - variables
- Command Results (implements [CommandResults](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-api/src/main/java/org/activiti/services/api/commands/results/CommandResults.java))
  - StartProcessInstanceResults
    - processInstanceId
  - SuspendProcessInstanceResults
  - ActivateProcessInstanceResults
  - SignalProcessInstancesResults
  - ReleaseTaskResults
  - ClaimTaskResults
  - CompleteTaskResults

## Message Enabled
By design a Runtime Bundle is enabled to consume and produce (async) messages. This is achieved by
using Spring Cloud Streams to consume Commands operations or emit Events representing the internal process engine
operations.

*Notice that a Runtime Bundle, by design, doesn't have WebSockets/Push Notifications built in.*

Events are emitted by the process engine using a message queue so external components can react to them.
All these events implements the [org.activiti.services.core.model.events.ProcessEngineEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-api/src/main/java/org/activiti/services/api/events/ProcessEngineEvent.java) interface.

These events can be found in the [activiti-services-events](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/) module.

* [ActivityStartedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/ActivityStartedEvent.java)
* [ActivityCompletedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/ActivityCompletedEvent.java)
* [ProcessStartedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/ProcessStartedEvent.java)
* [ProcessCompletedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/ProcessCompletedEvent.java)
* [TaskCreatedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/TaskCreatedEvent.java)
* [TaskAssignedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/TaskAssignedEvent.java)
* [TaskCompletedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/TaskCompletedEvent.java)
* [VariableCreatedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/VariableCreatedEvent.java)
* [VariableUpdatedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/VariableUpdatedEvent.java)
* [VariableDeletedEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/VariableDeletedEvent.java)
* [SequenceFlowTakenEvent](https://github.com/Activiti/Activiti/blob/master/activiti-services/activiti-services-events/src/main/java/org/activiti/services/events/SequenceFlowTakenEvent.java)

### Not covered Yet
* ActivityCancelledEvent
* ActivityCompensateEvent
* ProcessCompletedErrorEvent

## Activiti Services Data Types
- Core Types (org.activiti.services.core.model)
  - ProcessDefinition
    - id
    - name
    - description
    - version
  - ProcessInstance
    - id
    - name
    - description
    - processDefinitionId
    - initiator
    - startDate
    - businessKey
    - status
  - Task
    - id
    - owner
    - assignee
    - name
    - description
    - createdDate
    - claimedDate
    - dueDate
    - priority
    - processDefinitionId
    - processInstanceId
    - parentTaskId
    - status
  - ProcessInstanceVariables
    - processInstanceId
    - variables (Map)


## Requirements
Runtime Bundles then require the following list of infrastructural services:
- SSO / IDM service for handling Security and User/Groups resolutions
- Message Broker to emit and consume messages
- Database to store the state of the process instances and tasks


## Source Code & Docker Image

- [Runtime Bundle -> Source Code](https://github.com/activiti/activiti-cloud-runtime-bundle) Here you can find a Runtime Bundle example that you can use as the starting point to build your domain specific Runtime Bundles. This project was build using the [Runtime Bundle Starter that you can find here](https://github.com/Activiti/activiti-cloud-starters/tree/master/activiti-cloud-starter-runtime-bundle).
- [Runtime Bundle -> Docker Image](https://hub.docker.com/r/activiti/activiti-cloud-runtime-bundle/)
