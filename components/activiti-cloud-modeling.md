# Activiti Cloud Modeling

Activiti Cloud Modeling services provide the backend and frontend functionality to support our Activiti Modeler Application.

The backend services is currently composed by two Spring Boot Starters:

* [Activiti Cloud Org Service](https://github.com/activiti/activiti-cloud-org-service): provides the organizational structure for your Projects
* [Activiti Cloud Process Model Service](https://github.com/activiti/activiti-cloud-process-model-service): provides the interfaces to store models, independently of the storage that wants to be used.

The User Interface built with Angular can be [found here](https://github.com/activiti/activiti-modeling-app)

This application uses [BPMN.io](http://bpmn.io) for the BPMN process standard xml generation.

![New process modeling experience](../.gitbook/assets/screenshot-2018-12-21-at-15.13.44.png)

