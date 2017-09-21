# Runtime Bundle

Runtime Bundle is the Cloud version of the Process Engine.
Each runtime bundle will be able to host a set of fixed process definitions.
If you ever exposed Activiti as a service, you were using a Runtime Bundle.

Runtime Bundles in the context of Activiti Cloud represent a stateless instance of the process engine
which is in charge of executing an immutable set of process definitions.
## REST APIs (HAL)
Runtime Bundles expose a REST API with the following endpoints:
- /vX/process-definitions/
- /vX/process-instances/
- /vX/tasks

## Events



## Message Enabled
By design a Runtime Bundle is enabled to consume and produce (async) messages. This is achieved by
using Spring Cloud Streams to consume Commands operations or emit Events representing the internal process engine
operations.

*Notice that a Runtime Bundle, by design, doesn't have WebSockets/Push Notifications built in.*

## Requirements
Runtime Bundles then require the following list of infrastructural services:
- SSO / IDM service for handling Security and User/Groups resolutions
- Message Broker to emit and consume messages
- Database to store the state of the process instances and tasks


## Source Code & Docker Image

- [Runtime Bundle -> Source Code](http://)
- [Runtime Bundle -> Docker Image](http://)
