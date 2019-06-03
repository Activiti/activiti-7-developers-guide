# Cloud Native BPMN support

The BPMN specification describe a large number of constructs \(bpmn elements\) that are allowed inside a Business Process defininition. While supporting this in Activiti 5.x and 6.x was all done by the Process Engine, in Activiti Cloud \(Activiti Core 7.x\) less elements are supported due the fact that we are now dealing with a distributed and highly scalable infrastrucuture. Elements such as timers, signals and messages now involve interaction with the infrastructure and other services to work correctly. For this reason, the first version of Activiti Cloud selected a sub set of these elements to build a solid base that can guarantee that the execution among a set of components in a distributed environment behaves as expected, can be monitored and traced when things go wrong.

As part of this initiative we defined comformance sets to validate different use cases composed by different combinations of BPMN elements. These comformance tests uses both the new Java API and the Cloud Native APIs to validate the correct behaviour of all our components. You can find the [conformance scenarios here](../comformance/README.md).

The list of supported BPMN elements in the 7.1.x Release Train is:

* Start / End Events
* SequenceFlows \(conditional, default\)
* Service Task
* User Task \(assignee, candidateUsers, candidateGroups\)
* Gateways: Parallel, Exclusive
* Call Activity
* Signal Intermediate Catch Event, Signal Intermediate Throw Event, Signal Boundary Event

These BPMN elements are currently available in the Activiti Modeler application, and we will enable more as we add more support, tests and comformance for each of the remaining elements.

The remaining BPMN elements will be covered by future releases. We value the community feedback and we will prioritize based on what the community think that should be supported first. You can find the roadmap and the issues created for supporting these elements here. If you don't find what you are looking for please create a new issue and we will classify them accordingly. If you found the issue describing a BPMN element that you want to use please vote it up \(by adding a reaction or comments\) in github so we can prioritize based on this feedback.


