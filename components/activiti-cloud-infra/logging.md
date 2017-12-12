# Logging

The distributed logging example that we provided is based on the well known ELK(Elasticsearch,logstash,kibana) stack
and it runs in Kubernetes.

In the spring boot application examples, you will find the related configuration to run distributed logging on Kubernetes and to connect to Logstash.

The logback configuration file can also be enriched with further configurations about protocol(dp vs tcp), appenders
or the structure of the json log. For further informations please see:
https://github.com/logstash/logstash-logback-encoder

If you experience this issue https://github.com/docker-library/elasticsearch/issues/111
while executing the elasticsearch deployment descriptor in Kubernetes please see the solution here:
https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode

For example
```
minikube ssh
sysctl -w vm.max_map_count=262144
```
