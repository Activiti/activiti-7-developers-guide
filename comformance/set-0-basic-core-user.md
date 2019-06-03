# Basic Scenarios (ACTIVITI_USER Role API) 

Basic scenarios test the execution of BPMN elements in isolation. We should test happy and non-happy paths to understand which errors will be generated in case of issues during the execution. You can find the process definitions used for these scenarios here:

##Scenario Set 0: 
- [Process Information](https://github.com/salaboy/bpmn-scenarios/blob/master/processes/Process%20Information.bpmn20.xml) 
![](../.gitbook/assets/process-information.png)
  - Check that the process instance is COMPLETED (status) after start
  - Check that the process instance information is correctly propagated
    - businessKey
    - name
  - Start Process without Variables (StartProcess Operation)
    - Check that 8 events gets generated
      - PROCESS_CREATED
      - PROCESS_STARTED
      - ACTIVITY_STARTED
      - ACTIVITY_COMPLETED
      - SEQUENCE_FLOW_TAKEN
      - ACTIVITY_STARTED
      - ACTIVITY_COMPLETED
      - PROCESS_COMPLETED
  - Start Process with Variables (1 Variable)
    - Check that 9 events gets generated
      - PROCESS_CREATED
      - VARIABLE_CREATED
      - PROCESS_STARTED
      - ACTIVITY_STARTED
      - ACTIVITY_COMPLETED
      - SEQUENCE_FLOW_TAKEN
      - ACTIVITY_STARTED
      - ACTIVITY_COMPLETED
      - PROCESS_COMPLETED

- [Process with Generic Task](https://github.com/salaboy/bpmn-scenarios/blob/master/processes/Process%20with%20Generic%20%20BPMN%20Task.bpmn20.xml) 
  - Check that the process instance is COMPLETED (status) after start, BPMN Generic task doesn’t have any associated behaviour so the task is automatically completed by the engine
  - Check that the process instance information is correctly propagated
    - businessKey
    - Name
  - Check that 11 events are generated
    - PROCESS_CREATED
    - PROCESS_STARTED,
    - ACTIVITY_STARTED,
    - ACTIVITY_COMPLETED,
    - SEQUENCE_FLOW_TAKEN,
    - ACTIVITY_STARTED,
    - ACTIVITY_COMPLETED,
    - SEQUENCE_FLOW_TAKEN,
    - ACTIVITY_STARTED,
    - ACTIVITY_COMPLETED,
    - PROCESS_COMPLETED
- [Process Instance Delete](https://github.com/salaboy/bpmn-scenarios/blob/master/processes/UserTask%20with%20no%20User%20or%20Group%20Assignment.bpmn20.xml)
  - Start a process instance with a wait/safe state such as a UserTask
  - We will need to check for process events only, Task Events should be validated in Set 2
    - Start Operation
      - PROCESS_CREATED
      - PROCESS_STARTED
      - ACTIVITY_STARTED,
      - ACTIVITY_COMPLETED,
      - SEQUENCE_FLOW_TAKEN,
      - ACTIVITY_STARTED
    - Delete Operator
      - ACTIVITY_CANCELLED,
      - PROCESS_CANCELLED
  - After delete, we shouldn’t be able to find the process instance

- [Process Instance Suspend / Resume](https://github.com/salaboy/bpmn-scenarios/blob/master/processes/UserTask%20with%20no%20User%20or%20Group%20Assignment.bpmn20.xml) 
  - Start a process instance with a wait/safe state such as a UserTask
  - Start Operation
    - PROCESS_CREATED,
    - PROCESS_STARTED,
    - ACTIVITY_STARTED,
    - ACTIVITY_COMPLETED,
    - SEQUENCE_FLOW_TAKEN,
    - ACTIVITY_STARTED
  - Suspend Operation    
    - PROCESS_SUSPENDED
  - Resume Operation
    - PROCESS_RESUMED
