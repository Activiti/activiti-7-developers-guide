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
- [Basic Scenarios using the Local USER APIs](set-0-basic-core-user.md)
- Basic Scenarios using the Distributed USER APIs
- Basic Scenarios using the Local USER APIs with Data Handling
- Basic Scenarios using the Distributed USER APIs with Data Handling
- Basic Scenarios using the Local ADMIN APIs
- Basic Scenarios using the Distributed ADMIN APIs

The code for these tests can be found here:
- Activiti Core Conformance Scenarios
- Activiti Cloud Conformance Scenarios

These scenarios are used to gate our releases. This means that Activiti Dependencies and Activiti Cloud Dependencies are only released if these comformance tests are green. 

