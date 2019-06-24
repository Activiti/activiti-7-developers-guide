# Activiti Core FAQs

Q: we are trying to build gradle file with following dependency "compile group: 'org.activiti', name: 'activiti-engine', version: '7.0.0.GA'" I am trying with java 8 annd 11 both in local system. its build but when i execute in wildfly server 9.1 its give me error like " class file has wrong version 55.0, should be 52.0" .can you please suggest which version i used for build gradle and how to resolved this error.

A: this was fixed for 7.0.0.SR1, so we recommend to use this version or 7.1.x Release train

