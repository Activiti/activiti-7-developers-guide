# Cloud Connectors

Activiti Cloud Connectors provide an scalable way to perform bi-directional system to system interactions. It enable business processes to interact with external systems in a safe way without compromising the process runtime.

**No new frameworks, protocols, or any other crazy stuff was developed to bring Activiti Cloud Connectors to life**

Activiti Cloud Connectors provide:
- A quick way to map and perform a system to system interaction.
- A flexible configuration to support high demand scenarios using well known technology stacks
- A way to version and maintain multiple connectors up and running in the same infrastructure
- A default way to handle your BPMN Service Tasks
- An unified way to trigger actions in our Process Runtimes
- (Future) Inter Process Communications: signal/catch/send & receive messages


In order to illustrate the beauty of Activiti Cloud Connectors we will use the following scenario which demonstrate how these connectors can be used in fully containerized environments.

## Scenario (The WHAT)

You can find this example in our [Activiti Cloud Examples Repository](http://github.com/activiti/activiti-cloud-examples/trending-topic-campaigns)

Let's use the following Scenario to demonstrate all these concepts in action.

**Imagine that we are marketing company that looks into the Twitter stream to give rewards to the most active users on different trending topics. The company cannot predict which topics will be trending, they need to react quickly when they see a topic that can be used for marketing purposes. These marketing campaigns (based on trending topics) needs to be developed and deployed in minutes not hours. Each campaign will have a different set of rules on how to process Tweets and how the most active users will be rewarded for their participation. Because this is a global Marketing firm, we can assume that campaigns will be associated with just one language.**

In order to implement this scenario we can divide the requirements into two different big conceptual areas:
- **What** needs to be done: Filter tweets -> Process tweets to extract information -> Rank Users that created the tweets regarding a certain topic -> Reward top X users after a period of time.
- **How** it needs to be done: all the technical aspects of how to get the data moved to different services to perform the **What**

The **What** is usually referred as Business Logic or Domain Specific Logic. This is why our Marketing company is the best, because our Domain Experts master the **What**.
The **How** is what we developers & system integrators care about. We don't want to interfere with the **What**, quite the opposite, we want to make sure that as soon as the **What** is defined we can plug in all the technical bits so the company can have a new Marketing campaign up and running in minutes.

As part of the **How**, it is important to understand that all campaigns will share a common set of building blocks which we call infrastructure. These are message brokers, security mechanisms, etc. that will be share by all campaigns. For us to be fast and develop and deploy new campaigns quickly a running infrastructure must be in place so multiple campaigns can share the common bits. At the same time, it is important to notice that even if we share common infrastructural services, we should be able to scale each campaign independently.

Our Scenario will look like this:

![](../../../assets/examples/scenario.png)

The twitter stream will be consumed by our Activiti Cloud Twitter Connector. The connector here will be in charge of picking up each tweet and dispatch it as a message (throughout a message broker) to the Campaigns Pool. Campaigns interested in the tweet will be able to pick it up and process it accordingly. As mentioned before, campaigns are associated with just one language, but we can easily change this to be based on location or any other value provided by twitter API.

The Campaign Pool is dynamic by nature, new Campaigns can be added or removed at any time based on the needs of the company. Campaigns define how to deal with each trending topic that the company want to track and give rewards to their users.

![](../../../assets/examples/campaign.png)

Each campaign will define 2 main things:
- What is the campaign about, and how to process, analyze and rank each user involved in the trending topic at hand.
- How and when rewards should be delivered. This includes defining the number of top X users entitled to recieve rewards.


## Implementation (The HOW)

**Note Before starting:  it is important to understand that this example is complex because real life is complex. We tried to avoid too many hacks and shortcuts to make sure that we leverage the infrastructure as real applications (your implementations) will do. If you get overwhelmed with the amount of services and moving pieces you are probably not ready for uServices & Kubernetes just yet :). As part of the infrastructure we provide monitoring and tracing tools which will enable you to understand what is going on and how all these services are interacting.**

This section explains in detail the components required to create and run a new campaign.
The example is composed by 4 Maven projects that can be found here: [Trending Topic Marketing Campaigns Example](http://github.com/activiti/activiti-cloud-examples/trending-topic-campaigns)

- **activiti-cloud-connectors-3rd-party**: this Activiti Cloud Connector deals with how our business processes are going to interact with external services that we don't manage
- **activiti-cloud-connectors-ranking**: this Activiti Cloud connector deals with how our processes are going to interact with the Ranking Service to be able to reward users for the different campaigns.
- **activiti-cloud-connectors-twitter**: this Activiti Cloud Connector deals with all the tasks related to Twitter which includes: listening the twitter stream & enabling our application to tweet to users that receive rewards.
- **english-campaign-rb**: this is an example Campaign to deal with English Tweets and rewards, we encourage you to create new campaigns.


### Twitter Activiti Cloud Connector
First of all we need to tap into the Twitter Stream to consume tweets. For doing that we will use [Twitter4J]() and because we are consuming data from an external system we will create an Activiti Cloud Connector to deal with all the Twitter interactions.

Our Twitter Activiti Cloud Connector will be in charge of tapping into the Twitter Stream, route tweets based on their language and Tweet the rewards for the winners of each campaign.

 ![](../../../assets/examples/TwitterCloudConnector.png)

 In order to create this project we just need to create a simple Spring Boot 2 application and add our Activiti Cloud Connector Starter dependency to it. We also need the Twitter4J dependency and we are ready to go.

```
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

If you look at the Twitter Activiti Cloud Connector you will notice the following:
- We setup the Twitter4J Stream Listener to listen to all the global twitter Stream
- The LangAwareTwitterStatusListener is in charge of getting the language of the Tweet and adding it to the Header of the message. We use this as a filter for campaigns to quickly drop messages that are not matching with the campaign language.
- One Message Per Tweet is sent

As you can imagine this connector will end up containerized into a Docker Image that we can deploy and scale independently. We generate this docker image using the Fabric8 Docker Maven Plugin.

### Campaigns
Campaigns are domain specific uServices. Different marketing departments might need to create completely different campaigns based on their requirements.
As stated before in the scenario section, campaigns are responsible for:
- Defining how to process each tweet for a given trending topic
- Defining how to reward users which participated the most in the campaign


### Campaign Definition
We create a simple campaign composed by 4 main steps:
- Process the Tweet information
- Does this tweet Match the current campaign?
  - No Discard
  - Yes Continue
- Analyze the tweet
- Rank the Tweet Author

This is represented by the following business process:

![](../../../assets/examples/english-campaign-process-def.png)

Each campaign can include a variation of the process definition to deal with tweets in different ways. For the sake of simplicity we will use this process definition to deal with multiple campaigns. But you are free to change this definition and experiment with different requirements.

### Reward Definition

We defined a simple rewarding process as well. It is composed of 3 main steps:
- Get the Top X Ranked users for the given Campaign.
- Generate a reward per user.
- Tweet the reward to each user.

![](../../../assets/examples/english-campaign-reward-process-def.png)


### Campaign Runtime Bundle

In order to be able to execute these Business Process Definitions we create a new Activiti Runtime Bundle by creating a Spring Boot 2 application and adding our Activiti Cloud Runtime Bundle Starter as a dependency:
```
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-runtime-bundle</artifactId>
</dependency>
```

Then we copy our Business Process definitions under:
``src/main/resources/processes/``

The campaign runtime bundle project can be found here: [english-campaign-rb](https://github.com/Activiti/activiti-cloud-examples/tree/develop/trending-topic-campaigns/english-campaign-rb)

Both the Campaign and Reward processes will interact with external services to perform their tasks. This interaction will be handled by our Activiti Cloud Connectors. Which serves as an intermediate layer between the Process Runtime and the external service.

![](../../../assets/examples/campaign-cloud-connectors-services.png)

This intermediate layer of Cloud Connectors enable us to scale the process runtime independently from how they interact with other service. It also serves as a great place to add some technical and business details about each of these interactions. We need to consider that in real life interactions we might have SLAs and policies to limit or control these external services interactions. Activiti Cloud Connectors helps you to add these complex controls in a way that they are completely decoupled from your processes executions.

As you can imagine this Runtime Bundle will end up containerized into a Docker Image that we can deploy and scale independently. We generate this docker image using the Fabric8 Docker Maven Plugin.

## 3rd Party Service Connectors

## Ranking Service Connector
