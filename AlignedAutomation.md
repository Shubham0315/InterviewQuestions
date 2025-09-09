# ✅ Aligned Automation R2 Technical (11 Questions) 22 Minutes

From K8S perspective what standards do you follow for the on-premises deployments?
- 
- Set up HA K8S cluster (multiple master nodes). Have periodic etcd snapshots, multiple replicas
- Enable RBAC across all namepsaces on cluster. Secrets management  using K8S secrets or vaults or AWS Secrets manager
- Have service exposure standards like clusterIP for internal communication, LoadBalancer for external
- Define CPU and memory requests/limits for every pod. Use namespaces for logical isolation between env. 
- Use monitoring tools like prometheus for metrics collection, EKS for logging and use liveness/readiness probes for health checks for all pods
- Define proper storageClasses for dynamic provisioning
- Use CICD tools 
- Drain nodes gracefully, use rolling updates and pod disruption budgets
- Set Backup/restore policies for etcd and persistent volumes.

- Write yml files for resources needs to be created and set standards

---------------------------------------------------------------------------------------

How to use Hashicorp vault in CICD pipeline?
-
- Using vault in CICD pipeline is a secure and scalable way to manage secrets without hardcoding them in our pipeline code or config files
- Vault has secret engine enabled (KV - Key Value), policy and authentication methods
- Setup vault and authenticate pipeline via vault :- Enable auth method as "app role"
- Store secrets in vault
- Integrate vault with CI/CD tool. First install vault plugin in jenkins, configure vault server in jenkins by providing vault address and credentials in creds manager
- Use secrets in Build/deploy. So secrets are injected at runtime, never stored in repo.

- create policy, write policy and generate role, get role id and secrets ID, authenticate in pipeline
- Retrieve secrets in pipeline
- Use in CICD tools

- Create KV in vault and use in CICD pipeline

---------------------------------------------------------------------------------------

How to setting up splunk for any application?
-
- It involves configuring log forwarding, indexing and visualization so that we can collect, monitor and analyze logs in real time
- Install splunk enterprise for on-prem ir provision splunk cloud
- Configure index for application :- **index=app_logs**
- Decide what to log :- Access logs, error logs, custom app logs, security events, audit logs :- /var/log/myapp/app.log
- Splunk doesnt directly pull logs, it need forwarder. So, Install splunk universal forwarder on app server
- Define source types how to parse logs (JSON, CSV, syslog)

- We can also enable HEC (HTTP Event Collector) for kubernetes apps

- Then run index and validate results
- Create dashboards for KPIs like errors, response times, traffic. Define alerts

- "To set up Splunk for an application, we first create an index in Splunk, then install a Universal Forwarder on the app host or configure HTTP Event Collector for containerized apps. We define the sourcetype, configure inputs to monitor log files or streams, and send them to Splunk indexers. Once data is ingested, we validate via Splunk search and build dashboards and alerts for monitoring."

---------------------------------------------------------------------------------------

If there is performance related issue showing in splunk logs, how to troubleshoot it?
-
- Identify symptoms in logs like Slow response times, timeouts, High memory or CPU usage, frequent retries or failures
- Narrow down time picker/range
- Correlate logs across services like tracing request ID / correlation ID across services
- Check for known error patterns :- DB slow query logs, queue full, 
- Analyze custom metrics 

- Check if there is memory related issues and how many events for the same. Check for timestamp and related logs. If its not getting required memory requests or limits
- Check on which cluster we're getting error or on which service error is there

---------------------------------------------------------------------------------------

What are standard branching strategies used for an application?
-
- Standard branching strategies help us manage source code, releases and features efficiently.
- Git Flow :- Suited for apps with scheduled releases.
  - main 		:- prod code branch
  - development 	:- integrated branch for features
  - feature 	:- for new features
  - release 	:- for pre-release preparation
  - hotfix 	:- for critical prod fixes
 
- GitHub flow
- Single long branch is main. We can create feature branches from main
  - main :- prod ready code
  - feature :- short lived branches off main
  - PRs are merged into main
 
- Release Branching
  - Dedicated branch per release version
  - Allows maintenance/patching for older versions while new features continue on main
 
- Feature branching
  - Each feature gets its own branch
  - Merged after code review and testing
 
---------------------------------------------------------------------------------------

If I have created dev branch and deployed on dev env, if I want to move that code to staging env , how to do that?
-
- Merge dev to staging  (Option 1)
  - git checkout staging 		:- switch to staging
  - git pull origin staging	:- pull latest staging changes
  - git merge dev 		:- merge dev to staging
  - git commit -am "message"	:- resolve conflicts then commit
  - git push origin staging	:- push to remote staging

- Create PR from DEV to staging (Option 2)
  - Create PR in GitHub :- review changes :- merge PR after approval

- To promote code from dev to staging, we usually merge the dev branch into the staging branch and push it, which triggers the staging deployment pipeline. This ensures that only reviewed and tested code moves forward. In modern CI/CD, some teams don’t use separate staging branches but instead deploy the same branch (main) to multiple environments with different configs, controlling promotion through pipelines and approvals

---------------------------------------------------------------------------------------

How to maintain branching standards once testing is done and we want to move code from dev to staging?
-
- Follow standard branching workflow :- main, staging, dev, feature
- Ensure dev is up to date and stable
- Create PR from dev to staging
- Merge dev to staging
- Tag the staging release

---------------------------------------------------------------------------------------

Before setting up infra for an application, what is the process of standardizing any DR?
-
- Its critical to ensure business continuity, data integrity and minimal downtime in case of failures
- Understand business requirements like max downtime acceptable for users
- Perform risk assessment :- Identify potential failure points, evaluate impact on users
- Classify app components like backend, frontend, DB
- Choose DR strategy per components
  - For DB :- replication, periodic snapshots
  - For app :- multi region deployment or auto scaling
  - For storage :- cross region replication
- Then design DR architecture :- Use multi region or multi AZ design. Define failover mechanism like DNS switch, LB
- Standardize backup policies :- Frequency of backup, data retention period
- Use IaC for DR

---------------------------------------------------------------------------------------

Difference between clusterIP, nodeport and LB services?
-
- Cluster IP Mode
  - App exposes service inside the cluster only. Used for internal communication between services.
  - Assigns virtual Cluster IP to the service
  - Traffic flows from pod - Cluster IP - Kube proxy - Pod

<img width="721" height="324" alt="image" src="https://github.com/user-attachments/assets/fef1f2b1-e55c-46c9-a292-26c8ae7e875f" />

- Node Port Mode
  - Services allow apps to be accessible within organization or network
  - Anyone with access to worker nodes can access our app
  - Exposes service on eac Node's IP at static port (30000 - 32767)
  - Traffic flows from Client - NodeIP:NodePort - kube proxy - Pod
  - Works with cloud provider

<img width="754" height="338" alt="image" src="https://github.com/user-attachments/assets/2815151a-7854-4dba-8b47-ca6098207008" />

- Load Balancer
  - Provisions external LB like cloud and routes traffic into cluster
  - Used for internet facing apps in cloud environment
  - Traffic flows from client - Cloud LB - Node Port - Kube proxy - Pod
  - It auto balances traffic
 
<img width="725" height="311" alt="image" src="https://github.com/user-attachments/assets/055bcd94-b6df-4de1-bcc4-4dce438a5aa3" />

---------------------------------------------------------------------------------------

How to manage K8S DNS?
- 
- K8S uses CoreDNS as default DNS server. It runs as deploument in "kube-system" NS. It auto provides service discovery inside cluster
  - If we create service "myapp" in NS "dev", DNS name will be :- **myapp.dev.svc.cluster.local**
 
- DNS resolution in K8S :- Pods use cluster DNS by deafult
- Managing DNS config
  - Configure coreDNS :- edit coredns configMap inside "kube-system"
 
- In Kubernetes, DNS is managed by CoreDNS, which provides automatic service discovery. Services get DNS names like service.namespace.svc.cluster.local. Pods use this for internal communication. We can customize DNS policies in pod specs and manage CoreDNS via its ConfigMap

---------------------------------------------------------------------------------------

I have deployed microservice on K8S cluster and I dont want that cluster to be accessed outside environment. How to configure that?
-
- Use clusterIP service as its accessible inside cluster only to ensure no IP/Port is exposed
- Avoid node port and LB
- Use network policies for namespaces if we dont want all NS/PODS to reach that service
- Block external access at Ingress level
- Restrict access with firewalls/SGs

- To ensure my microservice isn’t accessible from outside, I expose it only as a ClusterIP service (no NodePort or LoadBalancer). If stricter control is needed, I apply NetworkPolicies to restrict which pods/namespaces can reach it.

---------------------------------------------------------------------------------------

# ✅ Aligned Automation R4 Technical (16 Questions) 34 Minutes

As you are using multiple services like VPC, what is the best architecture in terms of security and networking point of view if we need to setup 3 tier architecture in AWS? Where will you place EC2 in that 3 tier architecture?
-
- 3 tier architecture like web,app,db we can implement using VPC, subnets, routing and security controls
- VPC (Virtual Private Cloud)
  - Create one VPC per environment (dev,prod).
  - Define multiple sunets across at least 2 AZs for HA
  - Subnets can be public (for internet facing resources), private app subnets (for app layer EC2 instances or EKS nodes), private DB subnets (for databases like RDS, dynamoDB)
 
- For web tier, we can use ALB in public subnet which routes traffic to private subnet. No EC2 will be there in public subnet
- For app tier, we can have EC2 (or EKS services) inside private subnet which are only accessible via ALB. Outbound access via NAT gateway
- For DB tier, we can place RDS instances in private subnet (no public IP). SG allows traffic only from app tier

- Additionally we can configure SG and NACL with IAM policies
- We can also place Bastion host (jump server) in public subnet and restrict SSH access to private instances via it and SGs

- For a 3-tier architecture in AWS, I set up a VPC with public and private subnets across multiple AZs. The web tier (ALB) sits in public subnets, the app tier (EC2/ECS/EKS) in private subnets, and the DB tier (RDS) in private subnets with no Internet access. Security groups enforce tier-to-tier traffic only (ALB → App → DB). NAT Gateways in public subnets allow outbound traffic for patching. For security, I add WAF, IAM roles, encryption, and monitoring. This ensures isolation, least privilege, and high availability.

- We dont need to put our web server directly into public subnets because that increases the attack surface. Instead we place only ALB in public subnet, which is designed to handle internet traffic. Actual web app inside private subnet, only accessible from ALB via SG. Security is applied at ALB level rather than leaving app servers directly exposed.

---------------------------------------------------------------------------------------

How do you differentiate public and private subnet?
-
- Public Subnet
  - Its associated with route table that has route to an internet gateway (IGW)
  - Resources inside it like EC2, ALB can get public IP and can be accessed from internet if SG allow
  - Used as ALB, we can place bastion host here

- Priavte Subnet
  - Not directly connected to IGW
  - No direct inbound/outbound internet access
  - Outbound internet is routed via NAT gateway in public subnet
  - We can place app servers like EC2/EKS, DBs, which we dont want to expose
 
---------------------------------------------------------------------------------------

What is basic difference between Security group and NACL?
-
- SG works at instnace level while NACL works at subnet level
- SG is stateful and NACL is stateless
- By default in SG all traffic is denied except what we allow. In NACL all traffic is allowed until we add rules
- SG have only allow rules while NACL have allow and deny both
- in SG no rule priority, in NACL rules are evaluated in order from lowest to highest

- SG are stateful, instance level firewalls that only supports allow rules while NACL are stateless, subnet level firewalls that support both allow and deny rules
- SGs are used for fine grained resource access control like ALB while NACL are used for broader subnet-wide traffic filtering, such as blocking specific IP ranges

---------------------------------------------------------------------------------------

SG and NACL - which is stateful and which is stateless?
-
- Security groups
  - Stateful - remembers connection
  - If you allow inbound traffic on port, the response outbound traffic is auto allowed, we dont need to explicitly add outbound rules. Similar for outbound
  - If we allow inbound TCP 22 SSH from 10.0.1.10 and connect it to EC2, outbound from EC2 to 10.0.1.10 is auto allowed
  - Thats why SGs are simpler to manage
 
- NACLs
  - Stateless - dont remember connection
  - If we allow inbound traffic, we must also explicitly allow corresponding outbound traffic, otherwise return packets are dropped.
  - Both inbound and outbound need to be configured
  - NACLs are more detailed but harder to manage
 
---------------------------------------------------------------------------------------

If you have written shell script to take backup and have scheduled shell script via cron job that runs at some time everyday, how can we ensure that script is running everyday? Also how we get to know that its running at the specified time?
-
- We can monitor cron logs at "/var/log/syslog or /var/log/cron" to see entry every day for our job
- Redirect script output to log file
  - Command :- **0 2 * * * /home/shubham/backup.sh >> /var/log/backup.log 2>&1**
  - >> appends STDOUT, 2>&1 appends STDERR
  - We can tail /var/log/backup.log 2>&1 to confirm if our cron runs
- We can set email notifications :- MAILTO="xyz@gmail.com"
- We can also append success message with timestamp inside backup.sh
  - Command :- **echo "$(date): Backup successful" >> /var/log/backup_status.log**

- To ensure a cron-scheduled backup script runs daily, I redirect its output to a log file and also add a success marker in a status log. I can verify runs from /var/log/cron and my custom logs. For stronger guarantees, I integrate with a monitoring system or a healthcheck service like healthchecks.io that pings me if the job doesn’t run. Additionally, I validate by checking that the daily backup file exists with the correct timestamp. This way, I can be sure the backup script is running successfully every day

---------------------------------------------------------------------------------------

Without checking log file where logs getting appended, can we check if script is running?
-
- We can confirm if cron scheduled script is running by checking active process list with ps, or monitoring backup file timestamps.

---------------------------------------------------------------------------------------

Datadog is agentless or agent based?
-
- Data dog is primarily agent based
- We can install datadog agent on our host like EC2, VM, container, K8S node.
- Agent collects metrics, logs, traces locally and forwards them to datadog

- However it also supports some agentless integrations via cloud provider APIs
- For on premises agent based, for cloud agentless

---------------------------------------------------------------------------------------

How do you monitor your K8S cluster using datadog?
-
- We monitor our Kubernetes cluster using Datadog by deploying the Datadog Agent as a DaemonSet on every node. The agent collects node, pod, and cluster-level metrics from kubelet and the API server. We also enable the Kubernetes State Metrics Core for workload health, and log collection for pod/application logs. For larger clusters, we run the Datadog Cluster Agent to reduce API load. The collected data is visualized in Datadog’s built-in Kubernetes dashboards, and we configure alerts for node failures, pod restarts, scheduling issues, and resource saturation. Additionally, we enable APM for application-level traces.

---------------------------------------------------------------------------------------

Suppose you have deployed application on EKS cluster and for some reason your pods are not able to communicate with each other. What could be the reason?
-
- This case usually points to networking issues in K8S cluster
- EKS uses Amazon VPC CNI plugin. If its misconfigured, pods may not get valid IPs. They're stuck in ContainerCreating or network plugin not ready
- In EKS each node has attached SG. If intra or inter-node traffic is blocked, pods cant communicate
- If NACLs for VPC subnets are too restrictive, pod to pod traffic can be denied
- If subnet runs out of IPs, new pods wont get assigned valid IPs
- Network policies can block pod to pod communications

---------------------------------------------------------------------------------------

Where can we check if inter pod communication is allowed?
-
- Inter-pod communication in Kubernetes is open by default, but it can be restricted by NetworkPolicies, AWS Security Groups, or NACLs in EKS. To check, I first look at existing NetworkPolicies (**kubectl get networkpolicy**) to see if ingress/egress rules are blocking.
- Then we can validate the security groups and NACLs for node ENIs in AWS to ensure they allow intra-VPC traffic.
- Finally, we can check and confirm pod IP allocation with kubectl get pods -o wide and run a test pod (like netshoot) to verify connectivity between pods.

---------------------------------------------------------------------------------------

What is the difference between LB service type and ingress? Why ingress is used if we have LB service type?
-
- **Load Balancer**
  - It creates external cloud provider LB
  - Exposes single service to internet with dedicated external IP
  - Used when we've to expose one app directly outside the cluster
 
- **Ingress**
  - Provides HTTP/S routing rules to multiple services behind single LB
  - Ingress controller sits at edge, receives traffic and routes based on hostnames and paths
  - Used when we have multiple services and want to expose them using single entry point
  - Supports path based, host based routing
  - Only works at L7
 
- A LoadBalancer service exposes a single application to the internet by provisioning a cloud load balancer. The issue is that if we have many microservices, each needs its own load balancer, which is costly and inefficient.
- Ingress solves this by acting as a Layer 7 reverse proxy with an Ingress Controller, allowing us to expose multiple services behind a single external load balancer. Ingress also supports features like TLS termination, host-based and path-based routing, making it more flexible for web applications

---------------------------------------------------------------------------------------

If I have one project folder and I want to use that same codebase on multiple environments like dev, UAT, prod, how can we do that in terraform?
-
- Using workspaces which lets us use same codebases but separate state files
- Each WS keeps its own infra state where we can parameterize env specific values as required
- Create new WS, select it, apply config, state file config will be limited to that WS only

- We can also use modules and separate env folders
- We can keep reusable modules in one place. Each env has its own folder calling those modules with diff vars

<img width="756" height="382" alt="image" src="https://github.com/user-attachments/assets/15113ed0-280d-4870-915e-5e7a338368d0" />


---------------------------------------------------------------------------------------

How do you maintain terraform statefile?
-
- On AWS S3 as remote backend

---------------------------------------------------------------------------------------

Is cloudwatch agent is mandatory to configure metrics/services monitoring?
-
- Its not always mandatory. It depends on type of metrics you want to monitor
- Many AWS services send default metrics to Cloudwatch automatically like EC2, lambda, CPU

- For custom metrics cloudwatch agent might be required like memory/disk utilization, swap usage

---------------------------------------------------------------------------------------

How do you secure credentials in jenkins?
-
- Store inside credentials manager and mask the secrets them to use in pipelines
- We can also store in external secret managers like AWS or hashicorp vault

---------------------------------------------------------------------------------------

If you have to deploy application on linux environment, how will you connect linux from jenkins?
-
- To deploy app on linux server we need secure way for jenkins to connect and execute commands

- SSH Connection
  - Install SSH plugin, configure linux server credentials (username+ssh private key) in jenkins credentials store
 
- Jenkins agent on Linux
  - Make linux server a jenkins agent so pipeline runs directly on linux node, no ssh required

- To deploy an application on a Linux environment from Jenkins, the most common approach is using SSH. We store the Linux server SSH key in Jenkins credentials and use the SSH Agent plugin to connect and execute deployment commands. Alternatively, we can configure the Linux server as a Jenkins agent node, so the pipeline runs directly on it. In some projects, we integrate Jenkins with Ansible or Terraform to handle deployments, which removes the need for direct SSH. In all cases, credentials are securely stored in Jenkins and injected only at runtime

---------------------------------------------------------------------------------------

