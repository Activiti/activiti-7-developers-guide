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

As part of the **How**, it is important to understand that all campaigns will share a common set of building blocks which we call infrastructure. These are message brokers, security mechanisms, etc that will be share by all campaigns. For us to be fast and develop and deploy new campaigns quickly a running infrastructure must be running so multiple campaigns can share the common bits to avoid us deploying everything for each campaign. At the same time, it is important to notice that even if we share common infrastructural services, we should be able to scale each campaign independently.

## Implementation



We will start by tapping into the global twitter timeline using the twitter4j APIs. And for that we will create a new Twitter Activiti Cloud Connector which will include the [Twitter4j]() dependency. It is important to notice that connecting to twitter and fetching twitters is not a Domain Specific problem that we are trying to solve. For that reason, the connector is considered a System to System integration point, which needs to be coded, tested and released by a developer.

![](/assets/TwitterActivitiCloudConnector.png)

The Twitter Activiti Cloud Connector is in charge of fetching Tweets from the Global Twitter timeline and request the creation of a Process Instance for each tweet that it consumes. The Twitter Connector will do a pre-filter based on the language of the Tweet in order to select the appropriate Process Definition to process the tweet. 



The next step is to define our runtime bundle, that it only should contain our process definitions that for this example looks like this:

The first process is in charge of interacting with a couple of services, which involve another cloud connector. Because we didn’t want to share the same classpath between our twitter connector and the dependencies required to interact with these 3rd party services we kept them separated.

Our 3rd party cloud connector is going to use the RestTemplate provide by spring to interact with the shout, beer and chuck Norris services. This connector will use histryx as a circuit breaker to make sure that if one of these services are down we can keep operating.

Our third cloud connector is the one emulating a ranking service which is in charge of ranking the filtered tweets. It is also in charge of giving prizes to our top 3 twitter users every 10 minutes by starting a process in charge of giving prizes.


We will use the following Services:
- Twitter Stream
- [SHOUTCLOUD: THE CLOUD THAT SHOUTS BACK](http://shoutcloud.io)
- https://punkapi.com/documentation/v2
- [Chuck Norris Jokes Api](https://api.chucknorris.io)


This example tries to demonstrate the different options that we can use to approach different problems. The following list of points are some of the decisions that we have made consciously while writing the example:
* Both process definitions lives in the same runtime bundle. This was implemented in this way to simplify the example setup. You can place each process definition in a different runtime bundle to decouple their lifecycle (how often they get updated). For this particular scenario it would make a lot of sense to separate these process definitions for scalability reasons. Notice the different nature of these 2 processes. There is one process definition that might be started thousands of times per minute and the other just once every 10 minutes. You will probably want to scale the one in charge of processing tweets having multiple instances of that runtime bundle. Based on the nature of the process that process tweets, you want to make sure that this process services is never down, at the end of the day if you are giving prices away you want to be fair.
* We could have implemented all service connectors in a single big cloud connector, but this would couple the logic, lifecycle and dependencies required to perform the interactions. We placed all the 3rd party connectors together because they are all rest calls, but notice that now if one of them is updated we will need to update the whole connector and their data types associated to those requests.  If we were using Feign or any other library to generate client stubs, we will also need to deal with the generated sources, which might clash and cause issues.
* 3rd party services may fail or might be limited by SLA (x transactions per second or per $) using a rate limiter become handy to make sure that we limit the amount of resources that we use but at the same time we keep efficiency up.
* The twitter connector has the flexibility to route different tweets (for example: different languages) to different processes. These processes can live in different runtime bundles which can be developed and maintained in different life cycles in complete isolation. The twitter connector can use centralised configuration management to change this routing while running without even restarting the service.
