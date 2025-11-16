# ✅ L1 HCL Technologies Technical ( Questions) 50 Minutes

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

