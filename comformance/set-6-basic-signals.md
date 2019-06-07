# Scenario Set 6: Signals
This scenario covers Signal events in different combinations. The [source code of these tests can be found here](https://github.com/Activiti/Activiti/tree/develop/activiti-spring-conformance-tests/activiti-spring-conformance-signals).

![](../.gitbook/assets/set-6-signal-throw.png)
- [Process throwing a signal event]()
  - **Start Process Operation**:
    - PROCESS_CREATED
    - PROCESS_STARTED
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - PROCESS_COMPLETED

![](../.gitbook/assets/set-6-signal-catch.png)
- [Process with signal intermediate catch event]()
  - We should have the following events after starting this process (process is waiting for matching signal):
  - **Start Process Operation**
    - PROCESS_CREATED
    - PROCESS_STARTED
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
  - **Signal Operation**
    - We should have the following events after a matching signal is sent:
    - SIGNAL_RECEIVED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - PROCESS_COMPLETED

![](../.gitbook/assets/set-6-boundary-event.png)
- [Process with signal boundary event]()
  - We should have the following events after starting the process
  - **Start Process Operation**
    - PROCESS_CREATED
    - PROCESS_STARTED
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - TASK_CREATED
  - We should have the following events after sending a signal matching with  boundary signal event:
  - **Signal Operation**
    - SIGNAL_RECEIVED
    - TASK_CANCELLED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - TASK_CREATED

![](../.gitbook/assets/set-6-start-signal.png)
- [Signal start event]()
  - We should have the following events when a signal matching the signal start event is sent:
  - **Signal Operation**
    - SIGNAL_RECEIVED
    - PROCESS_CREATED
    - PROCESS_STARTED
    - ACTIVITY_COMPLETED
    - SEQUENCE_FLOW_TAKEN
    - ACTIVITY_STARTED
    - ACTIVITY_COMPLETED
    - PROCESS_COMPLETED
