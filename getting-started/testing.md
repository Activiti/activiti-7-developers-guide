# Is it working?

How do you test all these services? You have two options for now, but more are coming, you can use \(Chrome\) [Postman](https://www.getpostman.com) Collection to hit the REST endpoints of these services and check that components such as Query and Audit are receiving events from our Process Executions or you can use our Demo Client App \(using Angular JS\) that will perform the security Web Flow, redirecting you to [Keycloak](http://www.keycloak.org/) UI for Single Sign On to all of our services. This simple application allow you to test the different endpoints to check that everything is working there.

## \(Chrome\) Postman Collection

Install Postman Chrome plugin, then download \(if you cloned the github repo for the cloud examples you already have this in your local environment\) the collection located here:[https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti%20v7%20REST%20API.postman\_collection.json](https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti v7 REST API.postman_collection.json)

Import it into Postman and then you can execute different request against these services, mostly going through the API Gateway.

Depending on your environment setup you might need to change the IP addresses of the URLs in each request:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-00-28.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-00-28/)

Notice that to perform any request you first need to get a Token, which will be added to all the following requests that you make against the services. The token is set to expire after 3 minutes, meaning that you might need to request a new token if you wait for longer than that between interactions.

Once you have the token you can interact with any the other services that you have deployed.

Notice also that based on the name of your runtime bundle the URL for such request might change. The API Gateway will use the name of your Runtime Bundle App to register a new route to it when it is deployed, you will need to adapt that accordingly.


[Demo UI](demo-ui.md)
