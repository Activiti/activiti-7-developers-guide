# Cloud Connectors

## Scenario

You can find this example in our [Activiti Cloud Examples Repository](http://github.com/activiti/activiti-cloud-examples/twitter-process-example)

Let's use the following Scenario to demonstrate all these concepts in action.

**Imagine that we are marketing company that looks into the Twitter stream to give rewards/prizes to the most active users on different trending topics. The company cannot predict which topics will be trending, they need to react quickly when they see a topic that can be used for marketing purposes. These marketing campaigns (based on trending topics) needs to be developed and deployed in minutes not hours. Each campaign will have a different set of rules on how to process Tweets and how the most active users will be rewarded for their participation. Because this is a global Marketing firm, we can assume that campaigns will be associated with just one language (just for the sake of simplicity).**

In order to implement this scenario we can divide the requirements into two different big conceptual areas:
- **What** needs to be done: Filter tweets -> Process tweets to extract information -> Rank Users that created the tweets regarding a certain topic -> Reward top X users after a period of time.
- **How** it needs to be done: all the technical aspects of how to get the data moved to different services to perform the **What**

The **What** is usually referred as Business Logic or Domain Specific Logic. This is why our Marketing company is the best, because our Domain Experts master the **What**.
The **How** is what we developers & system integrators care about. We don't want to interfere with the **What**, quite the opposite, we want to make sure that as soon as the **What** is defined we can plug in all the technical bits so the company can have a new Marketing campaign up and running in minutes.

As part of the **How**, it is important to understand that all campaigns will share a common set of building blocks which we call infrastructure. These are message brokers, security mechanisms, etc. that will be share by all campaigns. For us to be fast and develop and deploy new campaigns quickly a running infrastructure must be in place so multiple campaigns can share the common bits. At the same time, it is important to notice that even if we share common infrastructural services, we should be able to scale each campaign independently.


## Implementation

**Note Before starting:  it is important to understand that this example is complex because real life is complex. We tried to avoid too many hacks and shortcuts to make sure that we leverage the infrastructure as real applications (your implementations) will do. If you get overwhelmed with the amount of services and moving pieces you are probably not ready for uServices & Kubernetes just yet :). As part of the infrastructure we provide monitoring and tracing tools which will enable you to understand what is going on and how all these services are interacting.**

First of all we need to tap into the Twitter Stream to consume tweets. For doing that we will use [Twitter4J] and because we are consuming data from an external system we will create an Activiti Cloud Connector to deal with all the Twitter interactions.

### Twitter Activiti Cloud Connector
Our Twitter Activiti Cloud Connector will be in charge of tapping into the Twitter Stream, route tweets based on their language and Tweet the prizes for the winners of each campaign.

 ![](../../../assets/TwitterCloudConnector.png)

 In order to create this project we just need to create a simple Spring Boot 2 application and add our Activiti Cloud Connector Starter dependency to it. We also need the Twitter4J dependency and we are ready to go.

```
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

If you look at the Twitter Activiti Cloud Connector you will notice the following:
- We have a routing mechanism that based on each Tweet Language a process definition will be picked up to start a new Process Instance which will process the tweet.
- This routing can be changed during Runtime by accessing to the Connector's exposed Endpoints. In real life scenarios you might have a completely separated Service that deals with this routing logic, the connector can remain as a simple integration point.
- Even if we have a process definition reference, we don't know exactly where that Process Definition is hosted, meaning that the infrastructure will be in charge of sorting that out for us. This is one of the main aspects that contribute to the scalability of Activiti Cloud.

### Language/Campaigns Manager/Router Runtime Bundle

The Language Specific Process definition will look like:
- Select Campaigns
- Process Tweets for each campaign (Start Process Instance that represent the campaign)
  - If the tweet doesn't apply for the campaign end process instance
- Rank Twitter User for the selected campaign

Each language can include a variation of the process definition to deal with campaigns and tweets in different ways. For the sake of simplicity we will use this process definition to deal with multiple languages.

Notice that we have a lot of business logic in here, and for that reason we will create our first Runtime Bundle which will contain the previously described business process.

In order to do that we create another simple Spring Boot 2 application and we add our Activiti Cloud Runtime Bundle Starter as a dependency:
```
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-runtime-bundle</artifactId>
</dependency>
```

We also need to include our Business Process definition under: src/main/resources/processes/

This project is called: "Campaign Manager/Router Runtime Bundle" and you can find the sources [here]( ).

The next step, once the correct process instance for the language is started to process the tweet is to run through the campaigns service. This service will be in charge of looking into all the available campaigns and forward the tweet to all the campaigns that might apply. Remember, campaigns are registered by language and they are dynamic, the can come and go without notifying this process.

[IMAGE SHOWING PROCESS INTERACTING WITH CAMPAIGN SERVICE]

The connector will query the Campaign service to see which campaigns are currently enable for the selected language and request to start a new Process Instance for each selected campaign.

### Campaign Runtime Bundle
Each campaign will be handled by a different runtime bundle, allowing us to scale them independently when needed. The campaign itself is the representation of how the business wants to deal with each tweet for different marketing campaigns. For this reason, one or more business processes will be included in this runtime bundle to describe which external services should be used. As part of the campaign, different services with different SLA & pricing schemes will be consumed
 
