# ✅ L1 Allvue Technical ( Questions) 48 Minutes

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
 

------------------------------------------------------

