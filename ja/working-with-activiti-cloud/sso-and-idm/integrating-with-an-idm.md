# Integrating with an IDM

## Creating an Integration module

The default IDM is keycloak and we've created an integration module for it:

[https://github.com/Activiti/Activiti/tree/activiti-7-201709-EA/activiti-services/activiti-services-identity-keycloak](https://github.com/Activiti/Activiti/tree/activiti-7-201709-EA/activiti-services/activiti-services-identity-keycloak)

There's also an implementation based on a properties file:

[https://github.com/Activiti/Activiti/tree/activiti-7-201709-EA/activiti-services/activiti-services-identity-basic](https://github.com/Activiti/Activiti/tree/activiti-7-201709-EA/activiti-services/activiti-services-identity-basic)

Each implements three classes:

SecurityConfig, which is a standard spring class and not Activiti-specific

AuthenticationProvider, which is also a standard spring class but it calls Authentication.setAuthenticatedUserId to tell Activiti who is logged in.

UserGroupLookupProxy is used by Activiti to find the groups for a user \(e.g. to see if a user is a candidate for a task\).

## Do I have to create a module?

No, you can just implement the relevant classes in your Activiti application. You don't have to implement a UserGroupLookupProxy if you don't want to \(but then groups for users won't be looked up\). Activiti just needs to be told who the current user \(their ID\) is through Authentication.setAuthenticatedUserId.

## How did it work in previous versions?

In previous versions \(before v7\) the default identity implementation was in the engine database and so overriding it meant implementing entity manager and factory classes. See for example [https://github.com/qbast/activiti-keycloak/tree/master/src/main/java/org/activiti/keycloak](https://github.com/qbast/activiti-keycloak/tree/master/src/main/java/org/activiti/keycloak)

There's a migration module from the database version to keycloak available at [https://github.com/Activiti/activiti-compatibility/tree/master/activiti-db-users-to-keycloak-migrator](https://github.com/Activiti/activiti-compatibility/tree/master/activiti-db-users-to-keycloak-migrator)

