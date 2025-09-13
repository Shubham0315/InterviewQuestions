# ✅ MindBowser Technical (11 Questions) 22 Minutes

In K8S master node is the brain of cluster. In public cloud master node should be managed by cloud provider. If our master node goes down and application is not running properly. As a devops engineer how to ensure master node should not be down and if it goes down how it should auto heal?
-
- K8S master node runs key components like etcd, apiserver, scheduler.
- Worker nodes can continue running workloads even if master node is temporarily unavailable but we cannot schedule new pods or manage cluster

- In public cloud, cloud provider guarantees HA by running multiple replicas of control plane components across availability zones. If it goes down, cloud provider auto heals it

- For on prem K8S clusters to ensure HA of control plane
  - Run at least 3 master nodes in different AZs. Configure etcd as cluster not instance
  - Take regular etcd snapshot backup and store them inside S3
  - Use auto scaling groups to recreate failed master nodes
  - Manage infra via terraform scripts so destroyed resources can be recreated quickly
 
-------------------------------------------------

What is the Core DNS in K8S?
-
- Default DNS server for K8S clusters. It provides service discovery by resolving DNS names of K8S services and pods

- Apps usually communicate using service names, not IPs as IP can get changed. Core DNS ensures that these names resolve to correct pod/service IPs dynamically
- Pod requests DNS resolution which is forwarded to Core DNS pods which looks at cluster Service and endpoints and responds with cluster of pod IP for requested name

- Uses :- service discovery, pod DNS resolution, forwarding

- Its installed as part of cluster setup in on-prem or cloud

-------------------------------------------------

How will you manage LB in on-prem K8S?
-
- LB in on-prem are trickier than cloud as cloud providers have built-in LB integrations

- In on-prem if we create service of LB type, nothing happens as no cloud LB exists to provision
- To manage LB we can
  - Use external LB like F5, HAPROXY. We can point external LBs to nodePorts of K8S nodes
  - Use ingress controllers wich runs inside cluster and manages HTTP/HTTPS traffic. We expose ingressController with nodeport or external LB
 
-------------------------------------------------

If we have 5 apps on K8S cluster and have 5 individual services for them all of which exposed by clusterIP and we want to expose app via ingress. How to manage LB in that case?
-
- Here instead of exposing each service individually, we deploy one ingress controller like Nginx/HAproxy
- IC is exposed to outside world through LB
- Then configure Ingress resource that routes traffic based on host or path to correct service

<img width="675" height="125" alt="image" src="https://github.com/user-attachments/assets/ffed7930-d4a7-4906-8386-a8883c8a72fe" />

- To manage LB
  - Deploy IC. It runs as pods inside K8S and watches IR
  - Expose IC outside cluster
  - Create ingress rules to route requests based on conditions
 
- IC will watch for IR and we can configure rules in conf file of particular LB associated
- Also we can configure rules in Ingress.yml
 
-------------------------------------------------

What is NATting in K8S ? SNAT and DNAT
-
- Network Address Translation
- Process to modify source or destination IP addresses of network packets as they travle between pods, nodes, external clients
- Its required as pods have their own virtual IPs which are bot routable outside cluster/network
- To enable communication between pods, nodes, external world, K8S uses DNAT and SNAT

- SNAT (Source NAT)
  - Changes source IP of packet
  - When pod communicates with external world, pod's IP is replaced by Node's IP which ensures return traffic knows how to get back
  - Used in outbound pod to external traffic
 
- DNAT (Destination NAT)
  - Changes destination IP of packet
  - When service is exposed it has virtual IP. Traffic coming to service IP is DNAT'ed to one of the backend pod IPs
  - Used to route pod traffic
-------------------------------------------------

On public cloud EKS we have multiple node. So even if one node goes down we can have another node up and reattch to master node. But in on-prem we cannot do that. If on-prem node goes down, how to make that node up and reattch to master?
-
- In public cloud control plane is managed by AWS. We've auto scaling groups so new node comes up and joins cluster. So its automatically done

- If in on-prem worker node crashes, we need to bring its server up, reinstall K8S components and rejoin node to cluster

- Here always run more worker nodes than needed. Use RS/deployments so pods reschedule to other healthy nodes when one fails
- If we run multiple master for HA, put them behind HAproxy. So when worker comes up, it can always join using same VIP instead of specific master IP

<img width="724" height="254" alt="image" src="https://github.com/user-attachments/assets/5bb5a342-e365-4d97-a4bf-ec38a924999d" />

-------------------------------------------------

You have to deploy 2 apps on single EC2 instance in such a way that one is node js and second is java app. But these 2 apps need to be exposed on 2 different IP. How we can setup?
-
- By default EC2 has one primary private IP. We can also attahc secondary private IP to same network interface
- Go to EC2 - Network interfaces - Assign additinal private IP - Associate 2 elastic IPs if you need the public
  - Now EC2 can listen on 2 public IPs
 
- Now configure each app to listen onlu on its designated IP

-------------------------------------------------

Can we attach 2 EC2 IPs to single EC2 instance?
-
- Yes we can attach multiple IPs to single EC2 instance. This is called assigning secondary private IP address to elastic Network Intergface (ENI) of instance

-------------------------------------------------

What is OIDC?
- 
- OpenID Connect
- Authnetication protocol built on top of OAuth2. It allows K8S to use external identity provider to authenticate users of service accounts
- OIDC is common way to plug enterprise identity systems (SSO) in K8S

- When user tries to access cluster, they're redirected to OIDC provider. User authenticates with credentials SSO. OIDC provider issues ID token JWT. User presents token to K8S API server which validates token against OIDC provider. If valid K8S maps identity to RBAC roles and grants access

- OIDC is like centralized authentication for K8S SSO/MFA
- No need to create or manage users manually
- Integrates with corporate identity providers like Octa, Azure AD

-------------------------------------------------

How many types of authentication does AWS have?
-
- Root user auth :- full access to AWS, should be used for only account setup
- IAM user auth :- users created. We can use MFA
- IAM role auth :- Used for temporary access, used by AWS services, cross account access

-------------------------------------------------

Suppose we've pipeline using which we're building docker image and pushing it to ECR. Here we've to pass AWS credentials in form of access keys. But we dont want to pass credentials or store them on any provider/server. How we can achieve the successful pipeline without storing credentials anywhere?
-
- Instead of using AWS keys, use OpenID Connect (OIDC) + IAM role with STS
- Our CICD provider acts as OIDC identity provider

- Create IAM role for GitHub OIDC. CReate role with trusted entity and condition to restrict repo/branch. Attach ECR permissions

<img width="809" height="130" alt="image" src="https://github.com/user-attachments/assets/365438c0-c4c9-4fbe-a038-e9f28a96e598" />


-------------------------------------------------


