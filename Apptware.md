# ✅ L1 Apptware Technical ( Questions) 71 Minutes

With your learnings in GCP and working experience in AWS, what differences did you realize? What advantages does AWS have over GCP?
-
- AWS has more mature and extensive ecosystem with a wider range of services and integrations with DevOps tools like Code pipeline, code build and cloud formation. GCP offers capabilities through code build, deployment manager and cloud functions but AWS provides deeper enterprise support and more refined IAM and networking options
- In AWS services like EC2, ASG and ELB are very mature and offer fine grained control. GCP compute engine is simpler to setup but network config and IAM policies are slightly more abstracted
- AWS IAM is very detailed and customizable while GCP IAM is more resource hierarchy based
- GCP has simpler and more predictable pricing model which is good for startups and experimental projects. AWS pricing is complex but offers more cost optimization tools like savings plans, spot instances

---------------------------------------------------------

What do you mean by cost optimization in AWS? What techniques have you used to achieve the same?
-
- Cost optimization means using cloud resources efficiently ensuring we only pay for what we use while maintaining performance and reliability. It involves analyzing, monitoring and fine tuning services to reduce unnecessary spend and make better use of AWS pricing models

- Techniques
  - Monitored EC2 instances, EBS volumes using cloudwatch and cost explorer. Identified underutilized resources and moved them to smaller instances
  - Periodically cleaned up unused EBS volumes, snapshots and elastic IPs
  - Implemented automated scripts to stop non-prod instances during off hours
  - Setup AWS budgets and cost anamoly detection alerts for monthly spending thresholds

---------------------------------------------------------

What all services have you worked on in AWS?
-
- Frequent and general question

---------------------------------------------------------

If our application is using the ECR repository, but images of multiple apps are being pushed to same ECR repo. As ECR charges us on basis of the data stored, is there any way we can optimize the storage over there? Condition is we dont want to loose images of applications and optimize the cost
-
- ECR charges us based on total image storage in GB which includes all image tags and untagged layers across repos
- When multiple apps push images to same repo, storage can grow rapidly due to duplicate images, old versions, untagged images left after deployments

- **ECR Storage Optimization Techniques**
  - Use ECR lifecycle policies to auto expire old or unused images based on rules like keep N images, delete untagged images, retain images. This ensures most recent versions of each app image are kept while older versions are cleaned up automatically
  - Set tagMutability = IMMUTABLE to prevent overwriting existing tags to avoid unnecessary re-uploading of layers and unexpected duplicate images

---------------------------------------------------------

Why are you exposing applications on EC2 insated of fargate ?
-
- Both EC2 and fargate can host containerized or web apps, the choice depends upon control, cost, scalability and use case
- We're using EC2 as our app require more control over underlying infrastructure, custom networking. Persistent configs are easier to handle with EC2
- EC2 gives us better cost optimization, ability to use custom monitoring tools and file systems which are not supported by fargate
- EC2 is preferred when we've stateful or legacy apps not fully containerized, if we need to run custom scripts

- Fargate is preferred when we need fully managed serverless containers, workload is short lived and when we dont want to manage infrastructure entirely

---------------------------------------------------------

In your career have you taken a decision related to application architecture?
-
- In one of my projects, the app was hosted on single EC2 which caused deployment downtime and scaling issues. I proposed and helped implement containerized microservice setup using docker and EKS
- We re-architected the app to use ECR for image storage, Application Load Balancer for routing, and Auto Scaling Groups for elasticity. I also designed the CI/CD pipeline in Jenkins, integrating SonarQube for code quality and Terraform for infrastructure provisioning.
- This decision reduced deployment time from 20 minutes to under 5 minutes and enabled zero-downtime releases with blue-green deployment strategy. It also improved cost visibility and environment consistency across dev, QA, and prod

---------------------------------------------------------

In cloudwatch, what all things have you worked upon? 
-
- General question
- Metrics, log groups, dashboards, etc

---------------------------------------------------------

Have you worked on cloudwatch alerts? How did you configure them?
-
- Yes, I’ve worked extensively with Amazon CloudWatch to set up alerts for infrastructure and application monitoring.
- I configured CloudWatch alarms to monitor EC2 instance metrics such as CPUUtilization, DiskReadOps, and StatusCheckFailed, as well as application logs pushed via CloudWatch Logs.
- I created alarms that trigger SNS notifications whenever a metric crosses a defined threshold — for example, CPU utilization > 80% for 5 consecutive minutes. These alerts were integrated with our Slack channel using an SNS

- Cloudwatch - alarms - create - choose metrics - set threshold conditions - choose SNS topic for notification like email, SMS, slack

---------------------------------------------------------

In SNS what all options do we have for alerts?
-
- In Amazon SNS (Simple Notification Service), we can send alerts or notifications through multiple delivery options — called protocols — depending on how we want to notify users or trigger actions.

- Email :- send email to subscribers email addresses
- SMS :- sends SMS to mobile numbers, text messages for users if they cannot access service/site
- SQS Queue :- sends message to amazon SQS queue

---------------------------------------------------------

Did you face any challenges in setting up SMS notifications delivery?
-
- I did face some challenges related to region restrictions, cost control and message delivery reliability
- Our SNS SMS delivery was not region specific, not all regions support SMS sending. So we had to ensure SNS topic was created in a region that support SMS
- Another challenge was SMS spending limits. We had to open ticket to increase monthly spend quota for prod alerts

---------------------------------------------------------

How did you set up the subscribers list in SNS?
-
- We setup SNS topic first and then add subscribers based on notification type email, SMS or lambda
- We can subscribe multiple endpoints like email, SMS, lambda, SQS, HTTPS
- For email or SMS receipent must confirm subscription link
