# ✅ L1 Spryc Technical (11 Questions) 29 Minutes

Can you explain Role based access control with respect to IAM?
-
- RBAC is a way to manae access to resources based on roles rather than individual users
- We can assign roles to users/groups.

- User represent person or service that interact with AWS
- Group is a collection of users, we can attach policies to groups and users under the group gets those policies
- Role provides temporary permissions to communicate between services. Also used for external users access
- Policy is JSON document that define allowed or denied actions on resources

-------------------------------------

What is 3 tier application ?
-
- Its common architectural pattern in software development where app is divided into 3 logical layers (tiers) each with specific responsibility

- Presentation Tier - Frontend
  - User interface layer which handles displaying data and receiving user input
  - Communicates with app tier to fetch or send data
 
- Application Tier - Business logic or Backend
  - Handles business rules, processing
  - Receives input from presentation tier, processes it and interacts with DB tier
  - Can handle authentication, logging, validation
 
- Database Tier - Data Layer
  - To store and retrieve data
  - Communicates only wit app tier
  - Ensures data integrity, transactions and persistence
 
- User submits login → frontend sends credentials → backend checks DB → returns success/failure → frontend displays result

 -------------------------------------

 Where the application is hosted and how SGs are helping to restrict access on the app?
 -
 - Frontend - EC2, ALB and S3. Users can access via HTTP/S
 - Backend :- EC2, lambda. Handles business logic and API endpoints
 - Database :- RDS, DyanmoDB. Only accessible by app layer, not usually public

 - Fronedend SG allows inbound traffic on port 80/443 from internet. Outbound traffic allowed to app tier SF
 - Application SG allows inbound traffic only from frontend SG and outbound traffic to DB SG
 - DB SG allows inbound traffic only from app SG and no public access allowed

-------------------------------------

Can you explain difference between services modes node port and cluster IP in K8S and tell use cases?
-
- Frequent question

-------------------------------------

What was your contribution in configuring EKS cluster?
-
- Determined cluster sizing like nodes, instance types based on app requirements.
- Designed VPC, subnets, networking for HA across AZs, NAT gateways, route tables
- Used terraform to provision EKS cluster.
- Configured IAM roles for cluster control plane, worker nodes and service accounts
- Enabled logging and monitoring using datadog deploying it on all worker nodes as agent daemon set
- Deployed K8S manifests for services, deployments, CM, secrets, configured HPA and CA managing blue-green deployments to ensure zero downtime
- Also integrated CICD for auto builds, docker image puch to ECR and deployment using kubectl
- Automated manifest updates, rolling updates and monitoring pipeline success/failure

-------------------------------------

Are you handling graceful termination in K8S pods?
-
- When pod is deleted or scaled down, K8S allows it to shut down cleanly rather than abruptly killing container to ensure resources are released properly and no data loss occurs
- When we initiate pod deletion K8S sends TERM signal to container process and pods enters into terminating state
- TerminationGracePeriodSeconds defines how long K8S wait before sending KILL signal, default is 30 sec
- If container does not exit within grace period, K8S sends SIGKILL to forcefully terminate it

-------------------------------------

Why we use docker compose?
-
- Used to deploy multi container docker apps which needs to work together
- Three tier app
- Instead of manually writing dockerfile for each, compose allow to define all services in single dockerfile and manage them together
- It simplifies running, stopping, and networking containers, manages volumes and dependencies, and ensures consistent environments across development, testing, and staging.

-------------------------------------

What is multi stage build in docker?
-
- It allows you to use multiple FROM statements in single dockerfile
- We can separate build env from final runtime which reduces final image size and improves security
- Multi stage builds are used to have smaller images, have faster and secure deployments reducing attack surface
- Only necessary artifacts from build stage is copied to runtime and we can pass CMD/ ENTRYPOINT in runtime stage

- Its single file only, just stages will be multiple
- We use AS Builder to copy required artifacts from build stages to runtime stages

<img width="713" height="379" alt="image" src="https://github.com/user-attachments/assets/5d5ba320-29b4-410d-985d-635bdf73f2cb" />

-------------------------------------

How is structure of CICD pipelines at your organization?
-
- Frequent question
- Checkout - Build Image - Push to ECR - Deploy to EKS - Post build actions

-------------------------------------

How do you take care of security at your organization?
-
- Using IAM and RBAC for AWS and K8S
- Use SG and NACL to restrict traffic
- Deploy sensitive services to private subnets
- Use secrets manager, vault to store secrets
- Use minimal base images and scan images using sonarqube
- Enable datadog logs for auditing access and detecting anomalies
- Regularly update OS, dependencies and container images

-------------------------------------

What kind of automation you did with shell scripting?
-
- Automated log rotation and cleanup for servers :- **find . -type f -mtime +7 -exec rm -f {} \;**
- Log analysis and monitoring :- **grep "ERROR" /var/log/app.log | awk '{print $1,$2,$5}' > error_report.txt**


