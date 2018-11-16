# Community

We are a very open community and group of developers looking forward to interact with a wider community of developers that wants to push Activiti & Activiti Cloud forward. You can collaborate as an individual or as an organization that want to support the project. There are several ways of getting involved, but we recommend you to get in touch via [Gitter](https://gitter.im/Activiti/Activiti7)

## Activiti adopts Git Flow

As the team is growing fast, we have adopted Git Flow as our standard flow for working on new features. We are going to use the jgitflow maven plugin to drive the flow.

This means that we are not going to work on the master branch any more, master will be used for releasing and it will always contain a stable state of the project. In other words, master will be updated every month with our monthly release process. All the work now will be based on the develop branch, that you will find in all our repositories.

You can find more information about this plugin here: Atlassian JGITFLOW and workflow provides a very good and clear explanation about how the workflow works.

In order to contribute and send Pull Request to any of our repositories you will need to use the following maven goal making sure that you have an up to date develop branch:

* mvn jgitflow:feature-start Starts a feature branch

  This will create a feature branch for you to work, push your commits and then send a pull request. These maven plugin will ask you to provide a name for your feature branch and that should follow the following format --. We will then review your PR and use jgitflow:feature-finish to merge that pull request.

If you have questions about these changes, procedures for contributions drop us a line in our Gitter Channel: [https://gitter.im/Activiti/Activiti7](https://gitter.im/Activiti/Activiti7)

