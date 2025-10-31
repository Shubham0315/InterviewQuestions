✅ L1 Allvue Cognitive Assessment ]


# ✅ L2 Allvue Technical (15 Questions) 48 Minutes

What all AWS services have you used in your role?
-
- IAM, S3, EC2, Cloudwatch, Lambda, Code pipeline, ALB, VPC, Subnets, SG, NACL, Config, Cloudfront, RDS, ECR, ECS

------------------------------------------------------

Can you explain what kind of automations have you done using lambda or shell scripting?
-
- I have used AWS lambda and shell scripting to automate several operationsal tasks like EBS snapshot cleanup, S3 data archival, log rotation and triggering notifications for failed jobs
- These automations have reduced manual intervention and improved reliability in the environment

- Lambda
  - Old snapshots were consuming a lot of storage cost
  - We created lambda function to list EBS snapshots older than 15 days (describe EC2 and decsribe snapshots) which deleted them automatically and sends summary to mail or slack using SNS topic
 
  - Lambda triggered by cloudwatch alarms for EC2 high CPU or unhealthy ELB targets. It auto restarts EC2 and notifies us via slack
 
- Shell Scripting
  - To compress or archive logs older than 7 days and remove old log files automatically
  - Shell script to have periodic checks of services and send alert if any service is down
  - Shell scripti for automated backups, monitor memory/CPU
  - Check modified files in specific times

------------------------------------------------------

What language runtime are you using for lambda functions?
-
- Python runtime

------------------------------------------------------

How are you automating infrastructure provisioning?
-
- Frequent question
- Using terraform for infrastructure provisioning
- Wite terraform scripts for resources like EC2, VPC, SG, EKS, NACL and apply
- Use modules for easier apply

------------------------------------------------------

Whats the difference between Terraform and Cloud formation?
-
- TF can be used for multi cloud resource setup using HCL language. Works well with AWS, GCP, Azure. TF maintains state file locally or remotely using backend. Its generally faster. We can have reusable modules written

- CFT is AWS restricted only. Writtten in JSON or YML. CFT maintains state automatically. Limited to AWS with few third party integrations.

------------------------------------------------------

Explain terraform workflow
-
- Frequent question
- terraform init - terraform plan - terraform validate (optional syntax check) - terraform apply - terraform detsroy

------------------------------------------------------

What is terraform state file? What is best practice to maintain terraform state file?
-
- Frequent question
- To record the info of all terraform resources created
- Maintain it remotely using S3 as remote backend so that config applied will be auto updated in the state file.

------------------------------------------------------

You did a terraform apply and state file is updated. Now if in AWS UI you delete a resource. Next time when we run terraform code, what will happen?
-
- If we delete resource and run terraform plan it compares whats in config files and whats in state file. Also check AWS existing resources
- It will detect resource recoreded in state file no longer exist in AWS
- So it will re created that missing resource to bring infrastructure back in sync with .tf code and state
- Then it will update statefile with new resource ID and metadata

- Alternatively we can use terraform refresh to update state file with actual state in AWS. Deleted resource will be removed from state file

------------------------------------------------------

Can you explain autoscaling? What are the types?
-
- Frequent question
- Replica sets, HPA and VPA for pods
- Node or cluster autoscaling for nodes. Auto scaling groups

------------------------------------------------------

What is load balancing? What are types of LB?
-
- LB is used to distribute incoming network or app traffic across multiple servers or instances to improve app performance, ensure HA, prevent single server from being overwhelmed

- Based on OSI layers there are 3 types of LB
  - Application LB (ALB) :- Happens at HTTP/S layer. Traffic can be routed based on URL, host, path. Used for web apps and microservices. We can use ALB inside public subnet for traffic distribution
  - Network LB (NLB) :- Happens are L4. Used for apps where we dont want latency issues like gaming apps. Data transferred in small chunks of packets so that server wont consume all resouces and we get required response without latency. Handles TCP, UDP, ELS traffic
  - Gateway LB (GWLB) :- Used with virtual applicances like VPN, firewall. Integrates with palo alto firewalls.
 
------------------------------------------------------

Lets say you have many instances and there is ALB in place. How ALB knows the incoming request to be routed to specific EC2 or app?
-
- User request is received by ALB at L7 and it makes routing decisions based on ingress rules we configure
- We can use Ingress with Ingress controllers. Ingress have capability to route traffic to different endpoints or context path or services.
- Here we can configure rules inside config file of LB to let ALB know where to route request
- Use target groups like EC2, Lambda, IP to ensure only healthy targets receive traffic

- We can also configure listener rules for specific ports. Listener exmines request and applies listener rules

- We can also make use of service concept where we define labels and selectors.

------------------------------------------------------

Explain difference between SG and NACL
-
- Frequent question

------------------------------------------------------

Can you explain what is CICD and how do you setup in your project?
-
- Frequent question
- Define stages like checkout, build, test, push, deploy
- CI auto builds, test and integrate code frequently whenever dev push changes to SCR. Detects integration issues early and maintain code quality
- CDel auto prepeares app for deployment to any env but with manual approval before production. After CI when code i packages, artifact is deployed auto to staging or test env. On prod we make artifact ready which needs manual approval
- CDep auto deploys app to prod after all tests are passed. Every time code change passes test, it auto goes live

------------------------------------------------------

What is blue green and canary deployments? Which has downtime
-
- Frequent question
- Both are designed to have no downtime. Blue green can have microseconds of downtime

------------------------------------------------------

What kind of monitoring do you support and use?
-
- Frequent question for SRE
- Datadog, cloudwatch and splunk

------------------------------------------------------


# ✅ L3 Allvue Technical (15 Questions) 36 Minutes


# ✅ L4 Allvue Technical Assessment - Live Proctoring by Onsite Team - 6 brief questions of Cloud and 3 Hands-on questions of AWS

