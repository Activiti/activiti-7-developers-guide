# Activiti Cloud Connectors

Activiti Cloud Connectors provide a scalable way to perform bi-directional system to system interactions. It enables business processes to interact with external systems in a safe way without compromising the process runtime.

**No new frameworks, protocols, or any other crazy stuff was developed to bring Activiti Cloud Connectors to life**

Activiti Cloud Connectors provide:

* A quick way to map and perform a system to system interaction.
* A flexible configuration to support high demand scenarios using well known technology stacks
* A way to version and maintain multiple connectors up and running in the same infrastructure
* A default way to handle your BPMN Service Tasks
* A unified way to trigger actions in our Process Runtimes
* \(Future\) Inter Process Communications: signal/catch/send & receive messages

## Cloud Connector as BPMN Service Task

Cloud Connectors are the default implementation for BPMN Service Tasks when only the attribute `implementation` is defined in the process definition file. The sections below explain how runtime bundle and Cloud Connectors interact.

### Activiti Runtime Bundle side

The Runtime Bundle relies on [Spring Cloud Streams](https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/) to delegate the service task execution to a Cloud Connector. Once the process execution reaches a _Service Task_, an _Integration Request_ message is sent to a [dynamically bound destination](https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/#dynamicdestination) having the same name as the one defined in the attribute `implementation` of the related Service Task. For instance, in the following example a message will be sent to the destination `SendRewardToWinners`.

`<serviceTask id="serviceTaskSendReward" name="Tweet Winners" implementation="SendRewardToWinners" />`

After sending the integration request, the Runtime Bundle will wait for the _Integration Result_ sent by the connector once its work is completed. In order to receive the Integration Result back the Runtime Bundle will listen to the input channel named `integrationResultsConsumer`.

The runtime bundle starter \(`activiti-cloud-starter-runtime-bundle`\) will bring the following configuration for the `integrationResultsConsumer` channel:

```text
spring.cloud.stream.bindings.integrationResultsConsumer.destination=integrationResult:${spring.application.name}
spring.cloud.stream.bindings.integrationResultsConsumer.contentType=application/json
spring.cloud.stream.bindings.integrationResultsConsumer.group=${ACT_RB_APP_NAME:my-runtime-bundle}
```

### Cloud Connector side

#### Implementation

In order to implement a Cloud Connector acting as a Service Task we will need:

* Add a dependency to the connector starter:

```text
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

* Define a consumer channel, to receive the Integration Requests coming from the Runtime Bundle. E.g:

```text
public interface RewardMessageChannels {
    String REWARD_CONSUMER = "rewardConsumer";

    SubscribableChannel rewardConsumer();
}
```

* Inside your Configuration class implement the channels interface and instantiate the message channel using Spring Integration `MessageChannels`.

```text
@Configuration
public class RewardConfiguration implements RewardMessageChannels {

    @Bean(RewardMessageChannels.REWARD_CONSUMER)
    @Override
    public SubscribableChannel rewardConsumer() {
        return MessageChannels.publishSubscribe(RewardMessageChannels.REWARD_CONSUMER).get();
    }
}
```

* Implement `Connector` interface by the connector class and annotate it with `@ConnectorBinding` with `input` parameter. Implement the connector business logic and send the result back to the Runtime Bundle

```text
@ConnectorBinding(input = RewardMessageChannels.REWARD_CONSUMER, condition = "")
@Component(RewardMessageChannels.REWARD_CONSUMER + "Connector")
public class RewardConnector implements Connector<IntegrationRequest, Void> {

    @Override
    public Void apply(IntegrationRequest event) {
        tweet(event);
        return null;
    }

    public void tweet(IntegrationRequest event) {

        // business logic goes here

        // build and send result back
        Map<String, Object> results = new HashMap<>();
        Message<IntegrationResult> message = IntegrationResultBuilder.resultFor(event, connectorProperties)
                .withOutboundVariables(results)
                .buildMessage();

        integrationResultSender.send(message);
    }
}
```

_Note:_ `IntegrationResultBuilder` and `IntegrationResultSender` \(autowired\) are provided by `activiti-cloud-starter-connector`. `IntegrationResultSender` will use dynamically bound destination again to make sure that the integration result message will be sent to the right destination: `integrationResult:<TARGET_APPLICATION_NAME>`, where `TARGET_APPLICATION_NAME` is the name of the runtime bundle which has sent the initial Integration Request.

#### Configuration

Eventually we'll need to configure the input channel declared above to receive integration requests only for the connector it's intended to. Remember that integration requests are sent to the destination defined by the attribute `implementation` of the related service task. In our example, `SendRewardToWinners`.

```text
spring.cloud.stream.bindings.rewardConsumer.destination=SendRewardToWinners
spring.cloud.stream.bindings.rewardConsumer.contentType=application/json
spring.cloud.stream.bindings.rewardConsumer.group=integration
```

**Important:** note that Cloud Connectors and the Runtime Bundle can \(and probably will\) run into different Spring Boot Applications / containers.

#### Error handling

From version `7.1.0-M7`, cloud connectors are able to notify the Runtime Bundle that something went wrong during their execution. This can be done in two ways:

* **Implicitly** by letting the connector throw an exception. This will trigger the Message Broker retry mechanism before notifying the error to the Runtime Bundle.
* **Explicitly** using `IntegrationErrorBuilder` and `IntegrationErrorSender` . This will not trigger the Message Broker retry mechanism: the error notification will be sent straight away to the Runtime bundle:

```markup
public void tweet(IntegrationRequest integrationRequest) {
    try {
        // business logic goes here
    } catch (Throwable error) {
        integrationErrorSender.send(
            IntegrationErrorBuilder.errorFor(integrationRequest, connectorProperties, error)
                .buildMessage());
    }
}
```

When the Runtime bundle receives a notification of an error occurred in the connector, it will send an `INTEGRATION_ERROR_RECEIVED` event to query and audit. Audit will store this event in its event logs and query will update the status of the related `service task` to the error status.

#### Throwing BPMN Error Event

Using the same mechanism above it's possible to throw a BPMN Error Event from a cloud connector by  using a `CloudBpmnError`. This BPMN Error can be caught by any BPMN Catch Error Event in the process that is in the scope of the service task calling the connector. 

**Important:** the error code used in the `CloudBpmnError` should match with the one used by the BPMN Catch Error Event.

```markup
public void tweet(IntegrationRequest integrationRequest) {
    
    // business logic leading to a case that should throw a BPMN Error event
    
    CloudBpmnError error = new CloudBpmnError("BPMN_ERROR_CODE");
    integrationErrorSender.send(
        IntegrationErrorBuilder.errorFor(integrationRequest, connectorProperties, error)
            .buildMessage());
}

```

