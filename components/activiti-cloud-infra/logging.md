# Logging

The distributed logging example that we provided is based on the well known ELK(Elasticsearch,logstash,kibana) stack
and it runs on Kubernetes. 
Please find this in:
https://github.com/Activiti/activiti-cloud-examples/tree/develop/kubernetes/kubectl/logging

In the spring boot application examples, you will find the related configuration to run distributed logging on Kubernetes and to connect to Logstash. 
For instance please see this configuration from the runtime bundle:
https://github.com/Activiti/example-runtime-bundle/blob/develop/src/main/resources/logback-spring.xml


The logback configuration file can also be enriched with further configurations about protocol(udp vs tcp), appenders
or the structure of the json log. For further informations please see:
https://github.com/logstash/logstash-logback-encoder
