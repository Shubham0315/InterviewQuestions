# ✅ L1 Pivot Chain Technical (9 Questions) 19 Minutes

Can you explain any CICD process that you have implemented in your project?
-
- Frequent question
- Explain stages in pipelines like checkout using github, build and push using docker/ECR, deploy on EKS, etc if its using Docker, K8S
- Explain stages like checkout using github, build using maven, sonar scan for security checks, nexus for artifacts and deployment on servers

---------------------------------------------

Can you explain how to deploy Linux application in K8S?
-
- To deploy make sure we have running K8S cluster, kubectl CLI installed and configured, our app should be containerized and pushed to docker repo
- Then create K8S manifest file for deployment and use linux image into it
- Expose the deployment using service outside cluster, use nodePort mode
- Apply deployment and service manifests to create resources. Check the resources once created if running
- To verify app is running or not use :- minikube service linux-app or kubectl get svc linux-svc or curl http://<external_IP>

---------------------------------------------

Explain the difference between deployment, stateful set and daemon set in K8S
-
- Frequent question

---------------------------------------------

How do you handle secrets in K8S?
-
- Frequent question
- AWS Secrets manager parameter store and Terraform vault KV pairs to mount in container Filesystem

---------------------------------------------

What is the difference between K8S ingress and LB service
-
- Ingress manages HTTP/S routing at L7 app layer while service provides direct access to app from outside cluster usually at L4 network layer
- Ingress uses controller to route traffic to different services based on host/path rules while LB service creates cloud provider's external LB that forwards traffic to single service
- When we ave multiple apps or services and want to expose all using single external IP or domain we can use Ingress. When we want to expose one service directly to internet with its own external IP use LB service

---------------------------------------------

Can you explain process to deploy K8S cluster in an offline environment without any internet access?
-
- Offline env means we cant directly do apt install or use kubectl commands, do docker pull or init
- We must prepeare everything in an online machine then transfer packages and images to offline network

- Stages
  - Download all K8S binaries and images on an online machine and store them locally
  - K8S uses several container images for core components. Save all images and packages
  - Transfer via offline media like US, hard drive, offline artifact repo
  - Install packages on offline nodes under /usr/bin
  - Load images locally
  - Initialize cluster and deploy CNI
  - Join worker nodes
 
---------------------------------------------

How will you manage image pull issue is there from online access in K8S cluster?
-
- It means K8S cant pull container image from registry. This can be due to wrong image name, private registry which require authentication, network/firewall issue, image not available locally, misconfigured container runtime

- To check and troubleshoot
  - Check pod events using describe and check message for failing
  - Verify image name
  - Check network/registry access. There can be network or DNS problem.
  - If image is in private repo we need image pull secret to be created and that has to be used in our manifest files for authentication. Then redeploy
  - If nodes dont have internet access, peload images
  - Verify runtime logs
 
---------------------------------------------

If our worker node goes down in our K8S cluster, what steps would you take to troubleshoot the issue?
-
- Check if the worker node is **NotReady** or **unavailable** to serve requests using get nodes

- To torubleshoot
  - Check node status usingd escribe command. Look at events like network plugin issue, node unreachable
  - Check if server is reachable using ssh user@IP. If SSH fails, tere could be a network problem or firewall must be blocking SSH
  - Check service status if stopped or misconfigured
  - Check container runtime. CRI fails due to corrupted image layers, disk full or OOM
  - Verify network connectivity to control plane
  - Check disk and resource utilization
  - If node is down for maintenance, mark it unscheduleable
 
---------------------------------------------

How do you monitor the applications? What tools do you use?
-
- Frequent question
- Datadog, splunk and cloudwatch

---------------------------------------------

# ✅ L2 Pivot Chain Technical (16 Questions) 17 Minutes

How you created K8S cluster? Which tool did you use for production ?
-
- For learning and lab setups, I've used minikube to manually configure control plane and worker nodes. This gave me idea about components like etcd, kubelet and kube-proxy
- For production environments I've used managed K8S service AWS EKS. They handle control plane management, upgrades, HA automatically which is more reliable for production workloads

- Workflow
  - Defined cluster using Terraform or eksctl
  - Created VPC, subnets and SG or networking
  - Setup node groups with autoscaling enabled
  - Configured kubectl and updated kubeconfig to connect to cluster
  - Deployed workloads using CICD
 
---------------------------------------------

What is difference between deployment and replica sets?
-
- Frequent question

---------------------------------------------

Suppose my frontend service is not able to communicate ith backend service in K8S . What will you check?
-
- Check pod status. Ensure both are running. Check if backend is restarting due to crashLoop or OOM
- Check service config like service type. Ensure selector labels match backend pod labels
- Check DNS resolution inside frontend pod. Check coreDNS logs and its used for K8S service discovery
- Check network policies. Ensure ingress rule allows traffic
- Check cluster networking. Verify CNI plugin is healthy. Try pinging the pods from each other
- To check app level issues,  check logs

---------------------------------------------

What are different types of services in K8S?
-
- Frequent question

---------------------------------------------

How to check RAM on linux machine and also storage?
-
- free and df commands. Frequent question

---------------------------------------------

How can we check CPU cores in linux?
-
- lscpu command to list total CPUs and architecture

---------------------------------------------

How to check resource utilization of nodes in K8S?
-
- Use kubectl top command. It shows real time CPU and memory usage for all nodes
- To check resource requests and limits, use describe command
- We can also use datadog for production monitoring where dashboards setups give us node CPU/memory/disk usage, pod level graphs, alerts for high utilization

---------------------------------------------

Consider application is working seamlessly inside local network. Client is saying its working slow on their network. How to troubleshoot?
-
- Check if its a latency issue, timieout or data issue. Gather related metrics like logs, timestamps from client
- Check latency between client and server using ping, traceroute
- If app uses DNS name or LB, confirm DNS resolution time. Check is client is hitting nearest LB/region. Sometimes global DNS routing can cause latency
- Check if server performance drops dusring client usage. This include CPU/memory, app logs for slow queries, timeouts, DB slow query logs
- Client often use corporate networks with proxy, VPC or SSL which slows down HTTPS traffic
- Use monitoring tools to check latency per region

---------------------------------------------

How to build docker image?
-
- Docker file and commands
- Frequent question

---------------------------------------------

What is difference between ADD and COPY in docker?
-
- Both ADD and COPY are used to copy files/directories from your local system into the Docker image — but ADD has extra features that make it behave differently.
- COPY :- copies files and folders from local directory to image. Used when we need to copy local files
- ADD :- Copies files and also auto extracts compressed files like .tar, .tar.gz. Can fetch files from remote URLs

---------------------------------------------

What is difference between public and private subnet?
-
- Frequent question

---------------------------------------------

Suppose I am not able to login remote machine using SSH. What can we do?
-
- Basic network connectivity check using ping. If ping fails there can be network/firewall/VPC routing issues
- Check SSH port is open and reachable using telnet
- Check SG or firewalls. Cloud instance must allow port 22 from source IP or network
- Check authentication issues like private key or permissions

---------------------------------------------

How to check if port is open or not?
-
- Using lsof or netstat commands
- To check if a port is open, I use ss -tuln or netstat -tuln on the server to see if it’s listening. To test connectivity from another system, I use telnet, nc, or nmap. If it’s not reachable, I check firewall rules, security groups, and application-level bindings

---------------------------------------------

How to check if process is running or not in Linux?
-
- ps -ef | grep $process_name

---------------------------------------------

How can we check if docker container is running or not?
-
- docker ps command

---------------------------------------------

How to check IP address of machine?
-
- ip addr show
- hostname -i

---------------------------------------------

# ✅ L3 Pivot Chain CEO Round 
