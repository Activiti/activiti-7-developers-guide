# Activiti Core & Activiti Cloud Supported BPMN Scenarios
The purpose of this section is to describe step by step the scenarios that needs to be covered for the Activiti Core & Activiti Cloud releases. These tests are  automated to guarantee that in future releases we don’t introduce regressions that might break some/all of these scenarios. 

This document is divided into different sections focused on different aspects of the execution for local and distributed environments. This means that the tests should cover how the execution and state is kept in each different service involved in the execution. 

These scenarios also needs to define the datatypes/payloads that are sent and the expected output from the consumer point of view (meaning the one trying to access the state via the available APIs).
 
There are different dimensions that needs to be covered in order to have a sensibile view about what is working and what is not:
- Local vs Distributed
- User vs Admin APIs
- Security Policies Enforcement
- Basic, Medium and Advanced/Complex Scenarios
- Data Handling & Persistence
- Performance (that can be analyzed separately) 

Based on these dimensions, we will start by covering the different combinations in the following order:
- Basic Scenarios using the Local USER APIs (**ACTIVITI_USER** Role API): 
  - Basic scenarios test the execution of BPMN elements in isolation. We should test happy and non-happy paths to understand which errors will be generated in case of issues during the execution. 
  - [Set #0: Process Information](set-0-basic-process-information.md)
  - [Set #1: Service Task](set-1-basic-service-tasks.md)
  - [Set #2: User Task](set-2-basic-user-tasks.md)
  - [Set #3: User Task Assignments](set-3-basic-user-task-assignments.md)
  - [Set #4: Gateways](set-4-basic-gateways.md)
  - [Set #5: Call Activity](set-5-basic-call-activity.md)
  - [Set #6: Signals](set-6-basic-signals.md)
- Basic Scenarios using the Distributed USER APIs
- Basic Scenarios using the Local USER APIs with Data Handling
- Basic Scenarios using the Distributed USER APIs with Data Handling
- Basic Scenarios using the Local ADMIN APIs
- Basic Scenarios using the Distributed ADMIN APIs

The code for these tests can be found here:
- Activiti Core Conformance Scenarios
- Activiti Cloud Conformance Scenarios

These scenarios are used to gate our releases. This means that Activiti Dependencies and Activiti Cloud Dependencies are only released if these comformance tests are green. 

