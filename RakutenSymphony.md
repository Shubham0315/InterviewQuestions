# ✅ Rakuten Symphony Technical (11 Questions) 22 Minutes

Tell me about your work experience on K8S
-
- I have hands-on experience working with Kubernetes for container orchestration. I have deployed, managed, and scaled applications on Kubernetes clusters, both on-premise and cloud-managed services like Amazon EKS. I’ve worked on creating and managing manifests for Deployments, Services, ConfigMaps, and Secrets
- In addition, I’ve configured Horizontal Pod Autoscaling (HPA) and Cluster Autoscaling to optimize resource utilization
- Configured volumes like PV, PVC
- Day to day tasks are monitoring K8S clusters using splunk/datadog and troubleshooting issues

--------------------------------------------

Write the bases steps used in CICD pipeline and state plugins used
-
- We have used plugins like Git, sonarqube, nexus in our pipeline for SCM checkout, security scans, nexus

- Stages used are :-
  - Sourc stage where we define to checkout code from SCM
  - Build stage where we can write mvn commands to build application in a pcackaged form
  - Test stage
  - Code analysis using SQ
  - Nexus to store specific artifact version to be used for prod deployments
  - Deploy stage where we define path/server to deploy application on
 
--------------------------------------------

What is jenkins agent?
-
- Jenkins agent is a machine which connects to jenkins master to execute jobs
- Agent node actually runs jobs in jenkins pipelines
- Agents are used to
  - Distribute workloads across multiple machines
  - run jobs in different environments
  - Scale build horizontally for parallel execution

- When user triggers build, it gets scheduled on agent node
 
--------------------------------------------

How do you sandwich shell inside a pipeline?
-
- To run shell commands in jenkins pipelines, use them inside "sh" step

<img width="710" height="434" alt="image" src="https://github.com/user-attachments/assets/b3441939-94a9-4966-85e0-902caa0310f9" />

- sh runs single shell command
- sh ''' ... '''' --:- sandwiches multiple shell commands into tripe quotes
- These commands work only on linux VMs

--------------------------------------------

There is a plugin called active choice active parameter in jenkins. Why it is used?
-
- Active choice plugin in jenkins is used to create dynamic, interactive parameters for jobs/pipelines
- Normally jenkins parameters are static, we define them once and they dont change unless we edit the job
- Active choices lets us male parameters react to user input or generate values dynamically at runtime

- Used  to generate parameters dynamically instead of hardcoding values
- To filter options

- Exaple use cases
  - Populate environments dynamically :- dropdown that lists envs like dev,qa, prod
  - Build versions from artifact repo
 
--------------------------------------------

Write basic steps involved in writing docker file to build an image
-

<img width="773" height="457" alt="image" src="https://github.com/user-attachments/assets/a1d8a900-0cc5-4454-8a9b-b2101b039d0b" />

<img width="773" height="246" alt="image" src="https://github.com/user-attachments/assets/2a18e639-0a39-4276-8445-b3f82d8fef42" />

--------------------------------------------

What is the difference between COPY and ADD in dockerfile
-
- Both are used to copy files from host to docker image

- COPY :- just copies files/directories from build context (local) into image
  - Command :- **COPY app.py /app**
 
- ADD :- Can do everything COPY does plus extracts archives into container, supports remote URLs to pull files directly from internet
  - Command :- **ADD https://example.com/file.txt /app/file.txt**
 
--------------------------------------------

What is ENTRYPOINT in docker?
-
- Defines main command that will always run when container starts
- It sets default executable for container
- Unlike CMD, EP is not easily overridden while we do docker run. We need to use --entrypoint

- ENTRYPOINT ["PYTHON", "APP.PY"]

--------------------------------------------

I dont want to recreate image after 2/3 months as it is having vulnerabilities. But I need to run one image which should have no vulnerabilities in future. You cluster is having internet access. How can you achieve that when our image runs it has latest packages without vulnerabilities?
-
-  A docker image is immutable, once we build it, the packages inside are frozen in that state. If you run same image months later, it still has old versions - vulnerable

-  To ensure my container always runs with the latest patched packages, I would modify the container’s startup (entrypoint/start script) to run **apt-get update** && **apt-get upgrade -y** so that every time the container runs, it pulls the latest packages from the internet before starting the application.
This way, even if the image was built months ago, it will update itself at runtime. However, in production best practice, I’d also set up an automated pipeline to periodically rebuild and scan images against vulnerabilities.

- We can also use base images that are continuously patched

--------------------------------------------

What is the difference between service and virtual service in K8S?
-
- Service is a native K8S object which provides stable endpoint for accessing set of pods
  - Handles service discovery resolving DNS name to pod IPs
  - Provides basic LB between pods
 
- Istio virtualService
  - CRD is provided by Istio
  - Used for advanced traffic management on top of K8S service
  - Lets us define rules like traffic routing like blue/green, mirroring traffic to another service

 - A Service in K8s gives you a stable way to reach pods. A VirtualService in Istio controls how traffic is routed to that service (like smart traffic rules on top of K8s services).

--------------------------------------------

If nodes are scattered in 2 different namespaces, how the pods in the nodes connect with each other?
-
- Namespaces are logical, not network boundaries. They organize resources but dont isolate network connectivity by default
- Pods in different NS can communicate as long as network policies or firewall dont block them
- Each pod has cluster IP. We can directly use pod IP or use service name

- K8S Services handle cross-namespace access. Instead of directly using pod IP, create service in other NS so pods in NS1 can use Service DNS to reach target pods

--------------------------------------------



