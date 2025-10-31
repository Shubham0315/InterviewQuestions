# ✅ L1 Infospica Technical (11 Questions) 25 Minutes

In past 6 months which tools have you used mostly? How are you deploying applications onto servers?
-
- Jenkins, EKS, DataDog, Splunk, GitLab
- Deployment is done using jenkins pipelines on EKS. In pipelines we're defining stages like checkout, build, push, deploy. We're hosting apps on EC2 servers

-------------------------------

Write deployment manifest to deploy 2 instances of nginx and explain the same
-
- Frequent question
- Use replicas to define 2 instances and write as general manifest

-------------------------------

How to manage security in kubernetes?
-
- To store secrets we can use AWS secret manager or terraform vault and mount secrets in filesystem to be used by pods
- To grant access to only authorized users make use of RBAC where define roles and bind them with service account to be used by pod
- To prevent unauthorized access to our servers we can make use of Security groups and NACL
- Frequent question
- We can also make use of IAM to restrict access

-------------------------------

Write jenkins pipeline stages
-
- Frequent question

-------------------------------

Write simple python docker file
-

<img width="850" height="196" alt="image" src="https://github.com/user-attachments/assets/15ed668b-661f-4b45-98fd-fc7135391baa" />

-------------------------------

Write a simple docker compose file for multiple services
-
- Docker compose is a tool used to build multi container docker apps. We can create multiple containers using single command and compose file
- Define services with names and use required images. We can also define ports, networks to be attached to services

<img width="1369" height="578" alt="image" src="https://github.com/user-attachments/assets/49d81426-7eae-4dbd-855d-374d285de5c1" />


-------------------------------

How to authenticate to dockerhub from jenkins
-
- Add docker creds in jenkins - Manage jenkins - credentials - system - global credentials
- Use them in jenkins pipeline using "withCredentials" block
- Never hardcode creds in jenkins file

-------------------------------

What is multi stage build in docker?
- 
- Frequent question
- To run app with minimal dependencies. In runtime only necessary artifacts will be carried for image to run
- Single file with multiple stages. We use AS Builder to copy artifacts between stages
- This enables our image to be lightweiht and containers to run efficiently without much load

-------------------------------

What is AWS VPC architecture?
-
- Frequent question
- VPC, public/private subnets, CIDR ranges, SG, NACL, EC2
- Deploying apps on worker nodes which are EC2
- Control plane is EKS
- Setup is done using terraform

-------------------------------

What is the difference between stateful and stateless in VPC?
-
- Frequent question
- Comes with SG and NACL
- SG are stateful and NACL are stateless
- SG is for instance level and NACL is for subnet level protection
- Stateful SG means if we allow inbound, outbound traffic is auto allowed
- Stateless with NACL means we need to explicitly allow outbound traffic for response to go out

-------------------------------

How to deploy app on production without downtime?
-
- Frequent question
- Use blue-green deployment or canary deployment
- For nodes upgrade use rolling update

-------------------------------

# ✅ L2 Infospica Technical (10 Questions) 21 Minutes

Can you explain more on the K8S experience? Which all components did you deploy from management as well as application perspective on EKS?
-
- Explain current application architecture

- Management perspective - Cluster Level components
  - Created EKS using terraform, configured worker nodes, IAM and SG
  - Integarted with VPC, subnets, route tables, SG. Configured CNI plugin for pod networking
  - Deployed ALB ingress controller for HTTP/S routing. Configured ingress rules
  - For monitoring and logging have datadog and splunk in place to give overview of metrics and dashboard
  - Secrets manager and vault is used for secrets management
  - Cluster autoscalar is deployed. HPA is in place
  - RBAC is configured - clusterRole, roleBinding and serviceAccount for teams
 
- Application perspective - Workload Deployment
  - Deployed microservices using deployment and RS
  - Used services like clusterIP for internal communication and LB/Ingress for public exposure of apps
  - Used PVCs backed by EBS volumes for DBs and EFS for shared volumes
  - For DBs have stateful sets in place
  - Datadog agent is deployed on all nodes as daemon sets

-------------------------------

Was your jenkins hosted on AWS or on any other env?
-
- It is a separate entity not hosted on AWS
- We had it running on an on-premises server / internal VM. The Jenkins instance was integrated with AWS services through secure IAM user credentials or access keys for tasks like pushing Docker images to ECR or deploying workloads to EKS.

- We had it on Linux VM, connected it to AWS CLI configuring it with IAM  keys via jenkins credentials store
- For artifact storage we used nexus artifactory

-------------------------------

How jenkins server was authenticated with EKS cluster for deployment?
-
- Jenkins pipeline needs permission to deploy resources to the EKS cluster

- Configured AWS credentials in jenkins so it will fetch cluster details, push images and deploy on EKS
  - Common way is with IAM user with access keys. Create user with limited access. Store access keys in jenkins credentials and use them in pipelines using "withCredentials" block

- Configure kubeconfig in jenkins
  - Command :- **aws eks update-kubeconfig --name my-cluster --region ap-south-1**


-------------------------------

Is only worker nodes are hosted in private subnet or master as well?
-
- Frequent question regarding VPC structure

-------------------------------

How are you managing ingress in cluster?
-
- We manage ingress using ALB ingress controller which directly integrates with EKS cluster and auto provisions ALB in AWS when we deploy Ingress resources in K8S
- This allows to handle application level routing HTTP/S for multiple services securely

- IC is Deployed as a Kubernetes controller in its own namespace. It watches for Ingress objects and provisions an ALB in AWS.
- IR defines routing rules (paths/hosts) to backend services within the cluster.
- ALB operates at Layer 7 (HTTP/HTTPS), perfect for routing to multiple apps behind a single entry point.

-------------------------------

Which ingress controller are you using for application?
-
- We’re using the NGINX Ingress Controller for managing application ingress in our EKS cluster.

-------------------------------

How is your work experience with ECS? 
-
- Frequent general question
- My experience includes creating ECS clusters, defining task definitions, and configuring services to run containers using Fargate as the launch type
- Mostly using ECR our team used to take pushed images to deploy on ECS. Thus we had integrated ECS with Application Load Balancers (ALB) for traffic routing
- I handled CI/CD deployments to ECS using Jenkins pipelines, where new images were pushed to ECR and automatically deployed to ECS services.

-------------------------------

How would you handle autoscaling for worker nodes?
-
- For managing autoscaling of worker nodes in EKS, I use the Cluster Autoscaler.
- It automatically adjusts the number of nodes in the node group based on pending pods or underutilized nodes.
- The Cluster Autoscaler is deployed as a Kubernetes deployment with the right IAM permissions to scale the underlying Auto Scaling Group (ASG) in AWS.
- We also define minimum, desired, and maximum node counts in the ASG to control limits, and use datadog metrics or custom scaling policies when needed to fine-tune performance and cost.

- Cluster autoscalar and ASG mainly

-------------------------------

How is your work experience on networking part like VPC, firewalls, WAF?
-
- I have hands-on experience working with AWS networking components like VPC, subnets, route tables, security groups, and NACLs to design secure and scalable network architectures.
- I’ve configured private and public subnets for EKS and other services, ensuring proper routing and isolation.
- For security, I’ve worked with Security Groups and Network ACLs to control inbound and outbound traffic, and I’ve implemented AWS WAF with ALB/CloudFront to protect applications from common web attacks like SQL injection and XSS.
- I’ve also configured VPC peering and endpoint gateways for secure service communication within AWS without exposing data over the internet.

-------------------------------

Have you worked on any security related services ?
-
- Frequent question
- SonarQube scanner

-------------------------------

