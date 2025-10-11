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

