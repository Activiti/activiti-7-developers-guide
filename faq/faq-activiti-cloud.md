# Activiti Cloud FAQs

Q: ELK setup is working now after using .nip.io instead of long hostname. question: how did you identify that this was due to long host?

A: The problem with using AWS NLB DNS name to install Activiti Helm chart in EKS is that it is not a wildcard DNS name. By resolving NLB DNS name to one of the IP addresses and using .nip.io wildcard DNS service does the trick, because Activiti Helm chart exposes services via dynamically created sub-domains, i.e. activiti-cloud-gateway...nip.io.

It is OK to use nip.io for quick testing, but the best best practice will be to register your own wildcard DNS A record for domain name that you own in Route 53 or your NS provider that uses AWS NLB DNS name alias with provided load balancing support.

Q: I am now installing the full example on GCP. Do you think that a Cluster like this: 4 vCPUs 15.00 GB is big enough? I am getting errors after a while of deploying the chart:

Warning FailedScheduling 44s \(x17 over 11m\) default-scheduler 0/1 nodes are available: 1 Insufficient cpu. It is better to have 8 CPUs \(i.e. 2 nodes with 4 CPUs each\) .

A: The recommended size in GCP is a 3 node cluster with n2-stardard-2 nodes

Q: Deployed the full-example on a GCP environment, but I can access the auth endpoint nor anything else. I can ping it, but the call hangs.

A: The external IP on GCP is provisioned dynamically when you install nginx-ingress that creates service with type LoadBalancer. You should pick and use nginx-controller 'External-IP.nip.io' to for deployment.

Q: I use \*.nip.io but to deploy Activiti using Docker Compose, but it is not working.

A: it's working now.. till now i was behind corporate network.. to test i disconnected it and connected from my phone. after that i updated .env with new ip that i got and ran make application and make modeler. now I can see it loaded projects and i was able to create new project as well.

