# ✅ L1 HCL Technologies Technical (13 Questions) 50 Minutes

How's your core experience in Kubernetes and Terraform?
-
- Frequent question

-------------------------------------

What is your experience in automating and optimizing deployments?
-
- Frequent question
- CICD automation for different pipeline stages
- Optimization of pipelines was mainly oriented to time and cost reduction. Explain how moving to cloud reduces cost and time

-------------------------------------

What do you mean by 99.9% availability in AWS? Have you encountered the situation of unavailability of any resources?
-
- 99.9% availability (also called three nines) means that the AWS service is guaranteed to be available 99.9% of the time in a given month. 99.9% availability means the service can be unavailable for around 43 minutes per month. AWS ensures that the service meets this uptime SLA, and if not, customers can request service credits

- I have seen situations where EC2 is unreachale due to AZ level network issues, EBS volume degradation
- During one of our deployments RDS experienced increased latency and heath checks failed. AWS auto performed multi-AZ failover but we had couple of minutes of downtime
- In one deployment there was a partial outage in one region so our pipelines depended on S3 got delayed. So outage lasted for 10 mins

-------------------------------------

What all the services have you done on cloudwatch?
-
- Frequent question
- Explain how different services create log groups and dashboard metrics
- Checking logs, monitoring metrics like CPU, memory, latency, response times, error rates
- SLA is 200ms but some requests throw more time, it is part of SLO/SLIs

-------------------------------------

What is your experience on Ansible?
-
- General question
- Explain the basic playbooks and yml files, how to define servers using parent child relation 

-------------------------------------

What is the recent automation you did in your current role?
-
- I have written some automation modules using terraform to create resources in AWS
- For one application I have configured LB, Auto scaling groups as well to route traffic to required services
- Automated CICD pipelines using jenkins

-------------------------------------

What if I delete PVC for a pod by mistake?
-
- When you delete a PVC (PersistentVolumeClaim), the outcome depends entirely on the Reclaim Policy of the underlying PV (PersistentVolume)
- Retain :- PVC is deleted but no data loss as we can bind PV to PVC again
- That pod will go to container creating or pending state and it will not start as volume doesnt exist
- Set **reclaimPolicy: retain** 

 -------------------------------------

 What do you mean by PV and PVC?
 -
 - Frequent question

-------------------------------------

What if we delete PV?
-
- It will NOT delete the actual storage backend like EBS. You must manually attach/detach storage
- Deleting PV simply removes K8S PV object not the physical storage

-------------------------------------

Under the prod namespace, I want to deploy one application?
-
- First create or update K8S namespace. If prod NS doesnt exist, create it
- Define application's deployment yml file and apply the same
- Verify the created deployment
- Create a service if app needs to be exposed to customers. Apply the service yml
- Monitor deployment using logs and status commands
- If we need to scale application :- **kubectl scale deployment my-app --replicas=5 -n prod**

-------------------------------------

What is CNI?
-
- In Kubernetes (K8s), CNI stands for Container Network Interface. It's a specification and a set of libraries used to manage networking for containers. CNI defines how networking for containers should be implemented and provides a standardized way for network plugins to integrate with container orchestration systems like Kubernetes.
- Kubernetes uses CNI to configure networking for containers, ensuring they can communicate with each other across nodes in a cluster, and also provide access to external networks.
- CNI plugins often handle the allocation of IP addresses for pods and containers. This can be done in different ways, depending on the plugin, such as assigning IPs from a pool or integrating with a cloud provider's network.
- e.g :- Calico, flannel, canal

-------------------------------------

What is Ingress Controllers?
-
- An Ingress Controller in Kubernetes is a component that manages and implements the Ingress resource, which provides HTTP and HTTPS routing for external traffic into your Kubernetes cluster.
- An Ingress is a Kubernetes API resource that defines how external HTTP(S) requests should be routed to the services within the cluster. It contains routing rules based on domain names, URLs, or paths. An Ingress resource doesn't handle the actual traffic itself. It only defines the rules. That's where the Ingress Controller comes in.
- The Ingress Controller is the actual implementation that reads the Ingress resources in the cluster and configures the underlying proxy or load balancer to handle external traffic accordingly. It watches for changes in Ingress resources and automatically updates its configuration to route traffic based on the defined rules.

-------------------------------------

If you want to copy a file inside a namepsace, which command will you use?
-
- kubectl cp $file $dest

-------------------------------------

