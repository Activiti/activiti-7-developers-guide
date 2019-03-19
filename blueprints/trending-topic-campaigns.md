# Trending Topic Campaigns

## BluePrint: Trending Topic Campaigns

Activiti Cloud BluePrints are designed to show users how different business scenarios can be implemented using Activiti Cloud Components. We started simple with the Trending Topic Campaigns example.

**Workshop** This BluePrint has a workshop associated with it that you can follow to get your Activiti Cloud application deployed in Kubernetes.

### For all the instructions visit: [TTC Docs Workshop](https://github.com/Activiti/ttc-docs/blob/master/workshop.md)

### Scenario \(The WHAT\)

Let's use the following Scenario to demonstrate all these concepts in action.

**Imagine that we are marketing company that looks into the Twitter stream to give rewards to the most active users on different trending topics. The company cannot predict which topics will be trending, they need to react quickly when they see a topic that can be used for marketing purposes. These marketing campaigns \(based on trending topics\) needs to be developed and deployed in minutes not hours. Each campaign will have a different set of rules on how to process Tweets and how the most active users will be rewarded for their participation. Because this is a global Marketing firm, we can assume that campaigns will be associated with just one language.**

In order to implement this scenario we can divide the requirements into two different big conceptual areas:

* **What** needs to be done: Filter tweets -&gt; Process tweets to extract information -&gt; Rank Users that created the tweets regarding a certain topic -&gt; Reward top X users after a period of time.
* **How** it needs to be done: all the technical aspects of how to get the data moved to different services to perform the **What**

The **What** is usually referred as Business Logic or Domain Specific Logic. This is why our Marketing company is the best, because our Domain Experts master the **What**.

The **How** is what we developers & system integrators care about. We don't want to interfere with the **What**, quite the opposite, we want to make sure that as soon as the **What** is defined we can plug in all the technical bits so the company can have a new Marketing campaign up and running in minutes.

As part of the **How**, it is important to understand that all campaigns will share a common set of building blocks which we call infrastructure. These are message brokers, security mechanisms, etc. that will be share by all campaigns. For us to be fast and develop and deploy new campaigns quickly a running infrastructure must be in place so multiple campaigns can share the common bits. At the same time, it is important to notice that even if we share common infrastructural services, we should be able to scale each campaign independently.

Our Scenario will look like this:

![](../.gitbook/assets/scenario.png)

The twitter stream will be consumed by our Activiti Cloud Twitter Connector. The connector here will be in charge of picking up each tweet and dispatch it as a message \(throughout a message broker\) to the Campaigns Pool. Campaigns interested in the tweet will be able to pick it up and process it accordingly. As mentioned before, campaigns are associated with just one language, but we can easily change this to be based on location or any other value provided by twitter API.

The Campaign Pool is dynamic by nature, new Campaigns can be added or removed at any time based on the needs of the company. Campaigns define how to deal with each trending topic that the company want to track and give rewards to their users.

![](../.gitbook/assets/campaign.png)

Each campaign will define 2 main things:

* What is the campaign about, and how to process, analyze and rank each user involved in the trending topic at hand.
* How and when rewards should be delivered. This includes defining the number of top X users entitled to receive rewards.

### Implementation \(The HOW\)

**Note Before starting: it is important to understand that this example is complex because real life is complex. We tried to avoid too many hacks and shortcuts to make sure that we leverage the infrastructure as real applications \(your implementations\) will do. If you get overwhelmed with the amount of services and moving pieces you are probably not ready for uServices & Kubernetes just yet :\). As part of the infrastructure we provide monitoring and tracing tools which will enable you to understand what is going on and how all these services are interacting.**

This section explains in detail the components required to create and run a new marketing campaign for a trending topic. The example is composed by 6 Maven projects that can be found here:

[https://github.com/activiti/ttc-\*](https://github.com/activiti/ttc-*)

* **english-campaign-rb**: this is an example Campaign to deal with English Tweets and rewards, we encourage you to create new campaigns with your own business processes and domain models.
* **activiti-cloud-connectors-processing**: this Activiti Cloud Connector deals with Tweets processing. E.g clean up unnecessary characters, analyse the sentiment of the content \(possitive / negative\).
* **activiti-cloud-connectors-ranking**: this Activiti Cloud connector is in charge of interacting with the Ranking Service to keep user ranks for different campaigns.
* **activiti-cloud-connectors-reward**: this Activiti Cloud Connector is in charge of rewarding top ranked users for each different campaign. This service should be used when we want to reward users that are engaged with a marketing campaign.
* **activiti-cloud-connectors-twitter**: this Activiti Cloud Connector deals with all the tasks related to Twitter which includes: listening the twitter stream & enabling our application to tweet to users that receive rewards.
* **activiti-cloud-connectors-dummytwitter**: this Activiti Cloud Connector acts as the previous one, but using pre-defined dummy tweets. It's mainly used for demo purposes. We created this connector to have more control over the rate in which tweets arrive and also to control a little bit the content of the tweets that might be found offensive for some audiences.

Besides that there are two more directories in this repository related with Kubernetes:

* **kubernetes/**: contains the kubernetes descriptor files for starting the infrastructure, cloud connectors and a campaign. it also contains the descriptors for deploying logging and tracing mechanisms to gain visibility about how the information is flowing between each of the deployed components.

## Campaigns

Campaigns are domain specific uServices. Different marketing departments might need to create completely different campaigns based on their requirements. As stated before in the scenario section, campaigns are responsible for:

* Defining how to process each tweet for a given trending topic
* Defining how to reward users which participated the most in the campaign

  ![](../.gitbook/assets/campaigns.png)

### Campaign Definition

We create a simple campaign composed by 4 main steps:

* Process the Tweet information
* Does this tweet Match the current campaign?
  * No Discard
  * Yes Continue
* Analyze the tweet
* Rank the Tweet Author

This is represented by the following business process:

![](../.gitbook/assets/english-campaign-process-def.png)

Each campaign can include a variation of the process definition to deal with tweets in different ways. For the sake of simplicity we will use this process definition to deal with multiple campaigns. But you are free to change this definition and experiment with different requirements.

### Reward Definition

We defined a simple rewarding process as well. It is composed of 3 main steps:

* Get the Top X Ranked users for the given Campaign.
* Generate a reward per user.
* Tweet the reward to each user.

![](../.gitbook/assets/english-campaign-reward-process-def.png)

### Campaign Runtime Bundle

In order to be able to execute these Business Process Definitions we create a new Activiti Cloud Runtime Bundle by creating a Spring Boot 2 application and adding our Activiti Cloud Runtime Bundle Starter as a dependency:

```text
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-runtime-bundle</artifactId>
</dependency>
```

Then we copy our Business Process definitions under: `src/main/resources/processes/`

The campaign runtime bundle project can be found here: [ttc-rb-english-campaign](https://github.com/Activiti/ttc-rb-english-campaign)

Both the Campaign and Reward processes will interact with external services to perform their tasks. This interaction will be handled by our Activiti Cloud Connectors. Which serves as an intermediate layer between the Process Runtime and the external service.

![](../.gitbook/assets/campaign-cloud-connectors-services.png)

This intermediate layer of Cloud Connectors enable us to scale the process runtime independently from how they interact with other service. It also serves as a great place to add some technical and business details about each of these interactions. We need to consider that in real life interactions we might have SLAs and policies to limit or control these external services interactions. Activiti Cloud Connectors helps you to add these complex controls in a way that they are completely decoupled from your processes executions.

We can easily build a docker image from our Runtime Bundle that we can deploy and scale independently if we are targeting an orchestrator such as Kubernetes. We generate this docker image using the Fabric8 Docker Maven Plugin, which is configured to generate the image after the Java application is packaged by maven. Notice that we can use the Fabric8 Plugin also to publish/deploy the image to a Docker Registry such as Docker Hub, which allow us to deploy all our components in a remote environment without the need of building each docker image locally in that remote environment.

## Activiti Cloud Connectors

Activiti Cloud Connectors provides a clean way to define system to system interactions. On this BluePrint Activiti Cloud Connectors are used heavily to feed the campaigns with data that needs to be processed by our Campaign's business processes. In the following sections we describe these connectors which all share the same approach.

### Twitter Activiti Cloud Connector

First of all we need to tap into the Twitter Stream to consume tweets. For doing that we will use [Twitter4J](http://twitter4j.org/en/configuration.html) and because we are consuming data from an external system we will create an Activiti Cloud Connector to deal with all the Twitter interactions.

Our Twitter Activiti Cloud Connector will be in charge of tapping into the Twitter Stream, route tweets based on their language and Tweet the rewards for the winners of each campaign.

![](../.gitbook/assets/twittercloudconnector.png)

In order to create this project we just need to create a simple Spring Boot 2 application and add our Activiti Cloud Connector Starter dependency to it. We also need the [Twitter4J](http://twitter4j.org/en/configuration.html) dependency and we are ready to go.

```text
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

You can find the source code of this connector [here](https://github.com/Activiti/blueprint-trending-topic-campaigns/tree/develop/activiti-cloud-connectors-twitter)

If you look at the Twitter Activiti Cloud Connector you will notice the following:

* We setup the Twitter4J Stream Listener to listen to all the global twitter Stream
* The [SocialFeedService](https://github.com/Activiti/ttc-connectors-dummytwitter/blob/master/src/main/java/org/activiti/cloud/connectors/twitter/services/SocialFeedService.java#L72) is in charge of getting the language of the Tweet and adding it to the Header of the message. We use this as a filter for campaigns to quickly drop messages that are not matching with the campaign language.
* One Message Per Tweet is sent via Spring Cloud Streams

### Dummy Twitter Activiti Cloud Connector

Alternatively you can use the Dummy Twitter component \(which is started by default in our deployment descriptors\) to simulate a controlled social media feed. We created this dummy component to control the rate in which tweets are fed in and also the content of those tweets so we can test and assert the behaviour of all the services.

You can find the source code of this connector [here](https://github.com/Activiti/ttc-connectors-dummytwitter)

This connector uses a Spring Data CrudRepository to store tweets in a database which reads to dispatch based on a rate that can be configured with a property called "**tweet.rate**".

This connector doesn't use Twitter4J because it simulates the feed with static data.

As all connectors it uses the **activiti-cloud-starter-connectors** to understand and work with IntegrationRequestEvents and IntegrationResultsEvents:

```text
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

### Processing Activiti Cloud Connectors

This connector simulates a service that clean up and execute a sentiment analysis for the content of the tweet. This allow marketing campaigns to see if there is positive or negative engagement to their campaigns. For perfoming the sentiment analysis it uses the **standford-corenlp** library

```text
<dependency>
    <groupId>edu.stanford.nlp</groupId>
    <artifactId>stanford-corenlp</artifactId>
    <version>3.8.0</version>
</dependency>
```

You can find the connector that does the Tweet Sentiment analysis here [TweetAnalyzerConnector](https://github.com/Activiti/activiti-7-developers-guide/tree/a169b3bebbeded9639076359738cd68397c81d30/blueprints/trendingtopiccampaigns.md) [https://github.com/Activiti/blueprint-trending-topic-campaigns/blob/develop/activiti-cloud-connectors-processing/src/main/java/org/activiti/cloud/connectors/processing/analyzer/TweetAnalyzerConnector.java\#L35](https://github.com/Activiti/blueprint-trending-topic-campaigns/blob/develop/activiti-cloud-connectors-processing/src/main/java/org/activiti/cloud/connectors/processing/analyzer/TweetAnalyzerConnector.java#L35)\)

As all connectors it uses the **activiti-cloud-starter-connectors** to understand and work with IntegrationRequestEvents and IntegrationResultsEvents:

```text
<dependency>
  <groupId>org.activiti.cloud</groupId>
  <artifactId>activiti-cloud-starter-connector</artifactId>
</dependency>
```

## Running the BluePrint

There are 3 ways of running this example depending on how familiar you are with containers and orchestrators:

* The Spring Boot way: so you will need to manually start each service plus some infrastructural components
* The Docker Compose way: we provide 3 docker compose files to bootstrap different uServices \(Infrastructure, Campaigns, Connectors\)
* The Kubernetes way: we provide 3 deployments descriptors to enable different aspects of the infrastructure \(Infrastructure, Campaigns, Connectors\)

We obviously recommend the Kubernetes way, the other two approaches might be used for development purposes but they lack of real life management features. Kubernetes is not the ultimate tool but it does quite good in abstracting us from the specifics of the IaaS.

You can of course run the application using thhe Spring Boot and Docker way, but the team is only working actively on the Kubernetes way. We also recommend to run these examples in a real Kubernetes Cluster, you can always get some free credit in the major Cloud Providers.

Look at [TTC-Docs](https://github.com/Activiti/ttc-docs/blob/develop/workshop.md) for further instructions.

