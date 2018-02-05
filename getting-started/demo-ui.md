

## Demo UI Client \(Angular JS\)

The other alternative, which looks more like building a client App, allows you to run an existing app that uses the Keycloak.js library to secure our rest calls and deal with requesting the token.

You can find this app in the demo-ui-client \([https://github.com/Activiti/activiti-cloud-examples/tree/master/demo-ui-client](https://github.com/Activiti/activiti-cloud-examples/tree/master/demo-ui-client)\) directory of the activiti-cloud-examples repository.

The README file in that directory guides you through the steps to execute the application.

Once you started the server you can access to:

> [http://localhost:3000/\#/main](http://localhost:3000/#/main)

In your browser and you should see something like this:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-07-45.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-07-45/)

This is Keycloak kicking in, asking you for your credentials. You can use **testuser/password** to login and proceed to the application:

[![](https://salaboy.files.wordpress.com/2017/09/screen-shot-2017-09-10-at-12-08-51.png?w=960)](http://salaboy.com/2017/09/11/activiti-cloud-meets-kubernetes-and-they-like-each-other/screen-shot-2017-09-10-at-12-08-51/)

Again, here you will need to replace to your environment IP depending what setup are you testing. If you are running with Docker Compose \(default URL to localhost\) you can go ahead and execute some requests.

Our suggested first step is to create a new Process Instance, then you can try the audit and query services for the sample date. Alternatively to the web browser UI you can use PostMan with the provided collection here:[https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti%20v7%20REST%20API.postman\_collection.json](https://github.com/Activiti/activiti-cloud-examples/blob/master/Activiti v7 REST API.postman_collection.json)

This should give you a high level idea about how to interact with these services and how to deploy a new Activiti Cloud Application when you need them.
