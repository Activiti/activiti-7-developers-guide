# **Using Keycloak to secure endpoints**

  


We don’t intend to document everything you can do with keycloak as the keycloak documentation is available for that and IDM in Activiti is pluggable. However we have chosen to provide keycloak integration out of the box so it would be useful to illustrate how to use it to do something like lock down that processes for a runtime bundle can only be started by users with a given role.

This involves changes in the IDM and in the client side.

## Client side

On the client side the change is in the property file -[https://github.com/Activiti/activiti-cloud-runtime-bundle/blob/master/src/main/resources/application.properties\#L19](https://github.com/Activiti/activiti-cloud-runtime-bundle/blob/master/src/main/resources/application.properties#L19)

We already have a constraint requiring that any access requires the user role:



keycloak.security-constraints\[0\].authRoles\[0\]=${ACT\_KEYCLOAK\_ROLES:user}

keycloak.security-constraints\[0\].securityCollections\[0\].patterns\[0\]=${ACT\_KEYCLOAK\_PATTERNS:/\*}  


So let’s add that a new role called ‘startProcess’ is required to start an endpoint that ends with ‘process-instances’:  


keycloak.security-constraints\[1\].authRoles\[0\]=startProcess

keycloak.security-constraints\[1\].securityCollections\[0\].patterns\[0\]=/\*process-instances  


This should match only to the endpoint for starting a process instance.

If you envisage making a lot of changes like this then you’ll want to use the spring cloud config server so you can change properties at runtime. Otherwise it’ll be a case of building and redeploying the app. Spring boot does let you set properties through environment variables by turning dots to underscores but that’s a bit awkward, is still a restart and we might forget we’d done that.

## **Server side**

We can get to the server-side admin functions by starting a keycloak docker container - preferably one already configured so for example we could just start the infrastructure for the docker example in activiti-cloud-examples. Then go to [http://localhost:8180/auth/admin/master/console/](http://localhost:8180/auth/admin/master/console/) and log in with admin/admin

Go to the springboot realm. Go to ‘roles’ and choose to add a role using the ‘Add Role’ button:

![](https://lh5.googleusercontent.com/zU7zW-N9p0VR6YBfEpH4R6az_rol9hS1Zcgsi4mxqD7i_NXCnTnJ0SVXEQ0ENhmP8ZaRJanlos3UPGRuxK9vPjdLAFUkL1hrcdONOWLZrD4QDJbfVVkPZa07IF5YOufy3FwCsBHx)

The role name needs to match the properties file so use ‘startProcess’ as before.

Note this is only being persisted to the docker container. If you want the role to be picked up when starting a new docker container then you’d need to build a new image with a modification to the realm son file \(which is exportable - export button at the bottom\) in the roles section -[https://github.com/Activiti/activiti-cloud-sso-idm/blob/bdde100e43afdafd1b45730d3671db3b61c978f5/springboot-realm.json\#L33](https://github.com/Activiti/activiti-cloud-sso-idm/blob/bdde100e43afdafd1b45730d3671db3b61c978f5/springboot-realm.json#L33)

To assign the role to a user go to user management and choose a user \(e.g. the testuser\) and under role mapping you can assign the user that new role.

\(The above can also be done in code if you don’t want to use the keycloak UI -[https://stackoverflow.com/questions/43222769/how-to-create-keycloak-client-role-programmatically-and-assign-to-user](https://stackoverflow.com/questions/43222769/how-to-create-keycloak-client-role-programmatically-and-assign-to-user). It’s also possible to assign the role to a group rather than a particular user.\)

## Putting them Together

The postman collection provided with the activiti cloud examples does a post to get a token using the user ‘hruser’. So if you get a token for that user and then try to start a process instance you shouldn’t be allowed. The user ‘testuser’, however, should be.

Notice that it’s just the one runtime bundle that we’ve applied the change to so we can control actions for one runtime bundle without applying the same config to any other runtime bundles \(if we want to\).



