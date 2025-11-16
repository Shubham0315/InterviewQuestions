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

