# Activiti Cloud Platform Common

There are some cross cutting concerns that all Activiti Cloud Services will share. The most basic example is how to deal with security and identity management \(meaning how to resolve who belong to which group\). Testing is also included here, as most of our services will require the same set of utility functions to perform some basic tests.

You can find these cross cutting concerns modules in the following repository: [Activiti Cloud Service Common](https://github.com/Activiti/activiti-cloud-service-common/)

## Remote Debugging

The following example components for activiti cloud are enabled for remote debugging:

Runtime bundles Query Audit Gateway Registry

However in order to use this the docker-compose file needs to be modified - for full instructions see [https://github.com/Activiti/activiti-cloud-query/pull/6](https://github.com/Activiti/activiti-cloud-query/pull/6)

## Security

See the security section of each component for access control policies. See also the sso-and-idm sections.

