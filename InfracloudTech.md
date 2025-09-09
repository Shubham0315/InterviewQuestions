# Infracloud Technologies Technical (42 Questions) 65 Minutes

What branching strategies do you use in your current project
-
- Git flow branching strategy :- release, prod, hotfix, dev

--------------------------------------------------------------------

What is the difference between rebase and merge in GIT?
-
- In GIT rebase and merge are 2 ways to integrate changes from one branch to another.

- **Merge**
  - Combines 2 branches by creating new merge commit that has 2 parents keeping history of both the branches intact
  - It is non linear but shows breanch structure with all commit history
  - We can merge feature branch to master
 
- **Rebase**
  - 2 step process :- add feature branch on top of master and merge feature to master
  - When we execute "git rebase master", git internally creates 2 new commit objects for 2 commits in feature discarding commits in feature. New commit objects will be created on top oif master
  - Then we need to merge them to master :- git merge feature
 
- When rebase is used, no chances of getting conflicts
- Everytime we can go for merge whether there are conflicts or not. After merging and resolving we can push to remote. In normal merge, history is also preserved
- Rebase can be only used within local. Only you are responsible for your changes and can create multiple branches and rebase them as needed. So advisable not to use rebase for remote repository as multiple developers can be using the master branch. In rebase history is not maintained as feature gets vanished

--------------------------------------------------------------------

What are the different deployment strategies?
-
-** Recreate (Basic) Deployment**
  - Stops old version completely and starts newer. Simple and straight
  - Downtime can occur, not suitable for prod. Used in small apps or non critical envs
 
- **Rolling Deployment**
  - Gradually replaces old instances with new ones, one instance at a time
  - No downtime, users can access app during deployment as well
  - Old and new versions run together, there can be compatibility issues
  - Used for apps where gradual rollout is applicable like K8S
 
- **Blue-Green Deployment**
  - Maintains 2 identical envs. One live and other for new version development
  - We can switch traffic from blue to green once ready
  - Minimal downtime and easy rollback as older version is there due to identical envs
  - Needs double resources, so costly
  - Used in HA prod systems
 
- **Canary Deployment**
  - Releases new version to small subset of users first, monitors and then gradually increases traffic
  - Reduces risk as can detect issues before full rollout
  - More complex setup, needs monitoring
 
--------------------------------------------------------------------

What is the jenkins architecture?
-
- Jenkins is CICD automation server that helps automate building, testing and deploying apps.
- Jenkins follow distributed, master-slave architecture

- **Master**
  - Central control unit
  - Used to schedule jobs on worker nodes, monitoring slaves, storing build results, providing web interface for managing jenkins and configuring jobs
 
- **Slaves**
  - Worker nodes that perform the actual build/deployment which master schedules on them
  - Executes build scripts, deployment commands, reports result back to master so master stores it
  - Agents are connected to master via SSH
 
- When multiple jobs are triggered simultaneously, they enter in queue in master and are executed on available agents based on priority and availability
- Each agent have executors which are slots to run parallel jos

--------------------------------------------------------------------

If you have hundreds of worker nodes and want to run hundreds of jobs concurrently with almost no queuing, how to do that in jenkins?
-
- Add more agents - Horizontal scaling
  - Each jenkins agent has limited no of executors to run jobs. If jobs exceed available executors they go in queue
  - So we can add more agents or increase executors per agent
 
- Use EC2/Cloud plugins (Auto scaling)
  - Jenkins master spins up new EC2 instances when queue builds up and shuts them down when idle
 
--------------------------------------------------------------------

What are different types of jenkins jobs?
-
- Jenkins job is a unit of tasks jenkins executes like building code, testing, deploying app.

- Freestyle :- Basic. We can configure job as needed. Pull code - Run commands - Archive results
- Pipeline :- Job defined as code in jenkinsfile used for CICD. Multiple stages
- Multi branch pipeline :- Auto discovers branches in Git repo and creates pipeline job for each branch with jenkinsfile
- Maven project :- for java projects, jenkins understand maven lifecycle.

--------------------------------------------------------------------

What is a multi-branch pipeline?
-
- Its a special type of pipeline job that auto discovers, manages and executes jenkins pipelines for multiple branches of repo.

- When we provide repo, jenkins auto scans it and for jenkinsfile in each branch it creates separate pipeline job
- When new branch is created or deleted in repo, jenkins updates auto via webhooks and periodic scans

- Imagine having main, dev, feature branches. Jenkins auto creates jobs for all

--------------------------------------------------------------------

What are different types of networks in docker?
-
- **Bridge**
  - Default networking in docker. Containers on same host communicate with each other in isolated network
  - If we've container mounted on host OS and we have to ping it from host, we get error as both container and host have different IP ranges due to different subnet
  - So docker creates virtual ethernet so container can talk to host. This is Bridge networking where veth acts as bridge
  - Containers on same bridge network can communicate with each other using container names where as containers outside network cannot access them directly without port mapping
 
- **Host**
  - Removes network isolation and binds container directly with host
  - When we create container, container shares host network NS
  - Here both host and container are in same subnet and have same IP ranges so we can ping as well
  - If user has access to host, he can directly access app inside container - Insecure
 
- **Overlay**
  - Enables multi host networking for docker swarm services. Useful in docker swarm and K8S when we've multiple hosts
 
- Commands :- **docker network create --driver=overlay shubham** (--driver not needed in bridge as default)

--------------------------------------------------------------------

Assume that you have a dockerfile in which you pass username and password. When doing docker inspect, we can see credentials. So how we can avoid this?
-
- If we put our creds in dockerfile, they get backed into image layers. So anyone with access to image can see them via inspect command. Even if we remove them, they remain in history layers

- Use docker secrets
  - Store secrets in docker secrets or K8S secrets
  - Secrets are mounted into container at runtime. So they're not visible in inspect
  - In ECR, we can use AWS system and secrets manager
 
- Use env vars at runtime
  - Dont hardcode dockerfile, pass at runtime
  - Or store in .env file
 
- Use external secret managers
  - Vault, AWS secret manager
 
- Multi stage builds
  - If creds are needed only for build time, we can use ARG in multi-stage builds and dont copy them to final stage
 
--------------------------------------------------------------------

What is difference between ADD and COPY in dockerfile?
-
- Both are dockerfile instructions that put files into image, but there are some differences

- COPY
  - Copy files/folders from local build context into image
 
- ADD
  - More powerful
  - We can extract tar archives automatically :- ADD app.tar.gz /usr/src/app
  - Can fetch files from remote URLs, downloads file from internet
 
--------------------------------------------------------------------

Can the ENTRYPOINT in dockerfile be overridden?
- 
- EP defines main executables of container which cannot be overridden easily using docker run unless we pass --entrypoint while running commands
- Current EP :- ENTRYPOINT ["echo", "hello"]
- This can be overridden :- docker run --entrypoint ls ubuntu

--------------------------------------------------------------------

What is multi stage docker file?
-
- Allows to use multiple FROM statements in single dockerfile
- Early stages are used for building, compiling, testing which include heavier images. While final stage consist of minimal runtime files leading to smaller and more secure image

<img width="743" height="295" alt="image" src="https://github.com/user-attachments/assets/5146ba76-8f43-46a7-ac89-e3d52986ad26" />

- We use AS buildder in first stage and only necessary artifacts are copied to final stage
- In 2nd stage we use --from=builder to copy artifacts

- Here compiler and build dependencies are not shipped, only binary is copied to final stage

- Multi stage provider clean separation between build and runtime env
- Saves space and increase security

--------------------------------------------------------------------

How many stages we can pass in multi stage docker file at max?
-
- No hard limit
- We can have as many FROM statements
- If more stages - bigger dockerfile - slow builds due to caching
- Each stage increase complexity

--------------------------------------------------------------------

While installing docker on local there are predefined storage area. If you want to alter it, can we alter?
-
- On linux docker stores images, volumes :- /var/lib/docker

- If /var run out of space, we need to change it

- Stop docker, move existing data, configure new storage path, restart docker and verify

--------------------------------------------------------------------

Please explain the K8S Architecture.
-

<img width="1244" height="376" alt="image" src="https://github.com/user-attachments/assets/445d18bc-7966-4c55-833f-04bf6da4f223" />

--------------------------------------------------------------------

What are the different CNI plugins available in K8S?
-
- CNI Plugins (Container Network Interface) provide networking capabilities to pods. Different plugins offer different features like network isolation, routing, policies, performance optimizations

- **Calico** :- Provides L3 networking, supports network policies for security and traffic control. Popular in prod clusters
- **Flannel** :- Simple overlay network. Easy to setup, used for basic pod-to-pod communication. No advanced network policies
- **Canal** :- Combo of Flannel and Calico
- **Amazon VPC CNI** :- Integrates K8S pods directly with AWS VPC networking. Each pod gets IP from VPC subnet, great for EKS clusters

--------------------------------------------------------------------

What are different CRIs (Container Runtime Interfaces)?
-
- K8S uses CRIs to talk to different container runtimes

- **Docker Dockershim** :- Used to be default runtime of K8S
- **containerD** :- Lightweight and prod runtime, used with cloud
- **CRI-O** :- Designed specifically for K8S. Lightweight, minimal runtime focusing only on K8S workloads

--------------------------------------------------------------------

Assume we've K8S cluster with multiple master and 5 worker nodes and we've app deployed on worker nodes. Application has 2 replicas. Application is exposed using nodePort service on 30000 port. The user is hitting the request on 30000 port on worker node 5. Will he get the response?
-

<img width="858" height="164" alt="image" src="https://github.com/user-attachments/assets/be810eca-03db-49ae-be26-f6564b29eacd" />

- When we expose service as nodePort, K8S opens same port on every node in cluster (master/worker)
- Kube proxy configures ip rules to route incoming traffic on that port to one of the backend pods behind the service

- If user sends request to worker5 : 30000
  - Kube-proxy on worker5 sees traffic on 30000 and forwards it to one of the available pods (on worker2 or 4)
  - Pod processes request and response is sent through worker5
 
- Yes, the user will get a response from the application, even though no pod is running on worker5.

--------------------------------------------------------------------

Assume the master node went down and we're hitting it with DNS service. Will we get the response back from application?
-
- If we've K8S cluster with multiple master for HA and 2 worker nodes with replicas, service exposed.

- DNS in K8S is managed by CoreDNS which resolves it to ClusterIP of service
- One master node being down wont affect as long as we have multiple masters as API is still available through another master node
- Here user request which goes to cluster IP and kube-proxy forward traffic to backend pods

- So user will get response when accessing app via DNS service

- DNS name resolvesto cluster IP - kube-proxy on worker keeps routing traffic to healthy pods

- If all master nodes go down, the API server and DNS service will fail → new requests may fail (DNS resolution issues, new pods can’t be scheduled, etc.).

--------------------------------------------------------------------

What are the different ingress resources you have used? SSL offloading is happening at SSL, I want it to happen at pod level. How to achieve this
-
- Ingress resources define how external traffic is routed to services inside cluster
  - Basic HTTP/S Ingress :- for host/path based routing
  - TLS/SSL :- TLS cert/key stored in K8S secret. Ingress controller terminates SSL at ingress layer
 
- Currently SSL offloading is at ingress means IC decrypts TLS traffic and sends plain HTTP to backend pods. We want End to end SSL where TLS should not terminate at ingress but pass through pod

- First enable SSL passthrough at Ingress Controller
- Configure pods/service for HTTPS as pods must have own SSL certs
- Alternative :- Skip ingress for SSL and use nodePort/LB with SSL passthrough so directly forward traffic to pods that terminate TLS

--------------------------------------------------------------------

Are K8S secrets safe to use?
-
- Yes for sensitive info. We can also use Hashicorp Vault as external

--------------------------------------------------------------------

I have an application to be deployed in which I have 3 replicas. All 3 should be deployed on different nodes. How we can achieve this?
- 
- Daemon set. But worker nodes should also be 3 as it will schedule replicas on each node

- **Pod Anti Affinity**
  - K8S scheduling feature which ensures pods dont get scheduled on same node
 
<img width="736" height="655" alt="image" src="https://github.com/user-attachments/assets/5d8e1eec-b372-45e7-878f-9dd191b80d5c" />

  - podAntiAffinity tells scheduler not to place pods with same label on same node
  - requiredDuringSchedulingIgnoredDuringExecution :- pods will not be scheduled together on a node

--------------------------------------------------------------------

State different volume access modes in K8S
-
- In K8S PV and PVCs define access modes to determine how volume can be mounted by pods

- ReadWriteOnce :- Volume can be mounted as read-write by a single node. Its most common mode for EBS block storage
- ReadOnlyMany :- Volume can be mounted read-only by many nodes simultaneously. Useful for sharing data which doesnt change
- ReadWriteMany :- Volume can be mounted as read-write by many nodes at the same time.
- ReadWriteOncePod :- Volume can be mounted by only one pod (even if multiple pods run on the same node). Provides stronger isolation than RWO.

--------------------------------------------------------------------

What is the role of kubeproxy?
- 
- Kube proxy runs on every node in K8S cluster
- Its responsible for networking, making sure traffic sent to service reached correct IP/pod
- Used for service discovery and LB. Ensures requests are forwarded to healthy pods (round robin)
- Implements K8S services
- If pod goes down it updated rules to route traffic to it

- Used to generate IP for pod

--------------------------------------------------------------------

Assume that you have a K8S clusyer with single master node. But on single master I need 3 etcd instances. How to achieve this?
-
- etcd is key value store that backs control plane. For HA we can have 3/5/7 etcds deployed

- We can run 3 etcd pods on one machine by
  - Gicing each etcd member a unique name, data-dir and listening ports
  - All 3 will for cluster but on same physical node
 
- But this setup will not give high availability. If master node crashes, all etcd members are lost

- So use 3 or 5 etcd members, each on a different master node. If you have only one master, then you should stick to a single etcd instance.

--------------------------------------------------------------------

Have you ever backed up etcd data?
-
- Yes – backing up etcd data is a critical part of Kubernetes cluster management because etcd stores all cluster state

- Use etcdctl which is official CLI for etcd
- We can automate backups via cronjobs - Setup K8S cronJob or external scheduler that regularly runs **etcdctl snapshot save** and stores snapshots in safe location
- Always take snapshot before upgrade of etcd or performing maintenance

--------------------------------------------------------------------

Explain flow of resource creation in terraform
-
- Write terraform config in .tf files using HCL
- Initialize terraform to download provider plugins and initialize backend to ensure terraform env is ready
- Run terraform plan to compare current infra state with desired, generates execution plan for resource config
- Apply changes. Terraform talks to cloud provider API or target syatem to update resources. Also updates state file with latest metadata
- TF stores current state of infra into state file which is used to track resources
- We can also have post creation steps :- Print output variables, run provisioners

--------------------------------------------------------------------

We created an EC2 using terraform to deploy application. Here I need to change some attributes and now using plan its suggesting for replace. When we do replace our app goes down. How to avoid this?
-
- Here replcae suggesting resource replacement which means we've to destroy and recreate EC2 again. So our app may go down

- To avoid this :-
  - **create_before_destroy** :- ensures new resource created first before destroying older one
 
<img width="714" height="206" alt="image" src="https://github.com/user-attachments/assets/39dcdd03-9fc0-4fd3-8663-38796cf5c625" />

  - **ingore_changes** :- To prevent TF from replacing resource

<img width="714" height="206" alt="image" src="https://github.com/user-attachments/assets/b2e231b2-9288-448b-b782-52b2333762e1" />

  - Blue-green deployment or rolling update


--------------------------------------------------------------------

What is the purpose of remote statefile?
-
- TF keeps track of resources it manages using statefile
- Statefile contains resource ID, metadata, dependencies between resources
- TF uses state file to determine what exist, compare with desired config, plan/apply config

- Remote state file is storing TF state outside our local machine like in S3
- Multiple engineers can work on same infra safely. Avoids state conflicts that happen if everyone uses local state
- Locking mechanism is there using dynamoDB to prevent simultaneous apply
- Works well for multi env setups

--------------------------------------------------------------------

What are taints in terraform?
-
- Taint is a way to mark a resource as needing to be recreated on the next terraform apply
- Sometimes resource is in bad state and we want to destroy and recreate it even if there is no config change
- Command :- **terraform taint <resource_type.resource_name>**

--------------------------------------------------------------------

You are a team of 10 people and 2 people does terraform apply at same time. How to prevent this?
-
- Use a Remote Backend with State Locking
- Use S3 with dynamoDB

<img width="718" height="225" alt="image" src="https://github.com/user-attachments/assets/12fd590c-1baf-474f-9b77-cf4f49665eb7" />

- When one engineer runs terraform apply, Terraform acquires a lock in DynamoDB. Any other attempt to apply will wait until the lock is released.

--------------------------------------------------------------------

In which file do you mention values of variables in terraform?
-
- Variables are stored in .tfvars file
- Terraform automatically loads terraform.tfvars if present.

- We can also pass values directly to command line :- **terraform apply -var="region=us-east-1" -var="instance_type=t2.micro"**

--------------------------------------------------------------------

If you want to submit something through your CI pipeline, then how to maintain TFVARS file as the values shouldnt be hardcoded each time?
-
- We can manage tfvars in CICD pipelines

- Use env vars

<img width="653" height="87" alt="image" src="https://github.com/user-attachments/assets/f42f7651-a629-4ed9-b0ae-d909434b5052" />

- Store .tfvars in Version control per env
- Use secret management systems

--------------------------------------------------------------------

What are modules in terraform?
-
- Modules help organize, reuse, and share infrastructure code.
- To create infra, we can create module named folder and put all files inside it like main.tf, input,output files
- terraform.tfvars will be placed at root level inside which values of variables are hardcoded along with source path from where we need to take reference of code config
- When we apply config, TF will fetch all config details from child folder and create infra accoprding to infra code.

- Using modules we can share reusable code to various teams to create their config just they need to modify tfvars files according to their requirements

--------------------------------------------------------------------

What is AWS cloudfront? How frequently is it good to refresh cloudfront at edge locations? How will it affect the performance?
-
- AWS Cloudfront is a Content Delivery Network service (CDN)
- It distributes content globally using network of edge locations to reduce latency
- Commonly used for static content like images/videos, dynamic content like API/web pages.

- Origin is source of content like S3, EC2, HTTP endpoint
- Edge locations are global caching nodes where content is stored closer to users
- Distribution - how CF serves content

- When content at origin changes, CF caches old version to edge locations
- Static content refresh - Long TTL like hrs to days
- Frequently updated content - Short TTL sec to minutes
- Critical issues - Immediately

--------------------------------------------------------------------

What is the difference between DB backup and RDS snapshot?
-
- RDS Backup - Automated
  - Daily automated backup of RDS instance which supports point in time recovery (PITR), we can restore DB to any second within backup retention period
  - Its fully managed and automated by AWA
  - Used for DR, compliance and ongoing protection of RDS instance
 
- RDS Snapshot - Manual
  - Its a user initiated, manual, storage level snapshot of RDS instance
  - We can only restore DB to the exact time when snapshot was taken
  - Stays in account until we manually delete it. We decide when to take
 
--------------------------------------------------------------------

Suppose you have EC2 server having volume of around 500GB. The volume is getting full when we deploy the application onto it. We have to increase size of volume, will we require restart or not?
-
- When we increase EBS volume size, no restart is required for EC2 as modifying EBS size is online operation. AWS Auto apply changes and new size is applied to OS

- We need to extend the file system inside the OS (Linux/Windows) to use the extra space
- Only if we're changing volume type from gp2 to gp3 or other, then restart is required

--------------------------------------------------------------------

How to enable/disable versioning in S3?
-
- Go to S3 bucket - properties - Bucket versioning - Enable (new versions kept for each object)
- Go to S3 bucket - properties - Bucket versioning - Suspend (disables new version creation)

- You cannot completely turn of versioning once enabled. We can only suspend it

--------------------------------------------------------------------

What is a NAT Gateway?
-
- Network Access Translation
- AWS Managed service allowing private subnet resources like EC2 to access internet
- Used for outbound internet access from private subnet

- After placing app inside private subnet, we create NAT gateway inside public subnet with Elastic IP
- Private subnet's route tables sends 0.0.0.0/0 traffic to NAT gateway and NAT Gateway forwards traffic to internet via Internet Gateway
- It maps IP of app with route table or of LB and get things from internet

- It supports TCP/UDP/ICMP

- e.g :- Web server in public subnet and DB server in private. If DB needs to download security patches from internet, we can configure NAT gateway so it can go out to internet securely without getting exposed

--------------------------------------------------------------------

What exactly is serverless in lambda?
-
- In serverless we dont configure/provision any servers/VMs, AWS handles all infrastructure.
- Lambda runs our code in response to events and scales automatically and we pay as we use
- Here code is event driven like S3 creation, EC2 deletion, any update, etc

- Suppose if we upload image to S3, lambda gets triggered which resizes the image and stores output in another bucket

- We dont even know where our app is hosted, IP of it

--------------------------------------------------------------------

How to check memory available in server? Also disk space
-
- free command to get RAM total, used, free, available
- top command to find top memory conusimg processes

- Disk space :- **df -h**

--------------------------------------------------------------------

How to set no of CPUs in server?
-
- On linux use **taskset** or **maxcpus** parameter

--------------------------------------------------------------------


