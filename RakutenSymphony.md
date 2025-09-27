# ✅ L1 Rakuten Symphony Technical (15 Questions) 26 Minutes

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

Lets say your backend service is running in NS1 and DB running in NS2. Now NS1 service has to get connection of DB all the time. Now consider 2 scenarios where DB is using node port and then cluster IP, DB port gets down and gets up. What will be the impact in both cases? What is the preferred way to get seamless connection in case our pod goes down?
-
- **Node Port Scenario**
  - NodePort exposes DB on static port on each node's IP :- nodeIP:nodePort
  - If DB pod goes down, node port is still open on node but no healthy pod is behind it.
  - Here NodePort ties dB to node IP and static port which can break if node fails, pod is rescheduled on other node
  - So node port is not flexible or stable for intra-cluster communication
 
- **ClusterIP Scenario**
  - ClusterIP provides stable DNS name and virtual IP inside cluster
  - Backend service connects via DNS
  - If DB goes down, cluster IP is still valid. As soon as new DB pod comes up, traffic auto flows to it
  - Connection may temporarily fail until new pod gets ready, but backend doesnt need to change anything like DNS or service abstraction
 
- So ClusterIP is the preferred way for seamless connection inside the cluster.

- Interview-ready Summary
  - With NodePort, backend must connect via <nodeIP>:<nodePort>. If DB pod dies, connection breaks until pod is back, and node issues can also cause downtime.
  - With ClusterIP, backend connects via a stable DNS name, and Kubernetes automatically updates endpoints when DB pods restart → seamless reconnection.
  - Preferred way: Use ClusterIP + Service DNS for intra-cluster DB communication, combined with readiness probes and retry logic, to ensure minimal downtime when pods restart.
 
--------------------------------------------

What is service account in K8S cluster?
-
- SA in K8S is an identity for processes running inside pods
- When pod runs, it may need to interact with K8S API or talk to other resources. Instead of using user's personal creds, pod uses SA

- Each NS has default SA, so pods in that NS runs with default SA
- SA can be linked to RBAC like roles/cluserRoles or RoleBinding/ClusterRoleBinding to control what pod can do

- Create SVC Account - Assign RBAC permissions - Use SVC account in pod

<img width="747" height="192" alt="image" src="https://github.com/user-attachments/assets/faaf9b10-a133-44cf-ad4e-8ea164fa85d5" />

<img width="837" height="652" alt="image" src="https://github.com/user-attachments/assets/e6d3b79c-383c-4d1b-b0a3-efef2ee8fa1d" />

<img width="760" height="334" alt="image" src="https://github.com/user-attachments/assets/0782feba-0afb-4d20-894b-b7bcff9bafe0" />

- Common use cases of SVC Account
  - For apps inside cluster to talk to K8S API securely
  - For external systems to authenticate with K8S
  - To securely get cloud creds
 
--------------------------------------------

Developer has developed a Java application. He has given all code to you and you've to put everything inside a container and create a pod out of it. You've to make sure that pod doesnt run without running the application and pod gets down as soon as app gets down. How we can achieve this?
-
- Create dockerfile for the java app

<img width="724" height="564" alt="image" src="https://github.com/user-attachments/assets/9610ac56-1f59-4549-befe-d8130b120912" />

- Here we've given entrypoint as java process itself so that if java app stops, container stops immediately

- Then create pod.yml

<img width="741" height="293" alt="image" src="https://github.com/user-attachments/assets/9d4b59b9-fbfd-486e-a2dd-98c90d395938" />

- When pod starts, container runs "java -jar myapp.jar"
- If app crashes or exits, container stops and pod goes down

- I’d create a Dockerfile with the application JAR and define **ENTRYPOINT **["java", "-jar", "myapp.jar"]**_. This ensures the container lifecycle is directly tied to the Java process — if the app goes down, the container stops, and hence the Pod terminates. In Kubernetes, I’d set restartPolicy to Never if I want the Pod to completely stop, or OnFailure if I want it to restart automatically. Additionally, I’d configure liveness/readiness probes so Kubernetes can detect app health.


--------------------------------------------

What is configMap?
-
- ConfigMap is a K8S API object used to store config data like key-value pairs, config files, env vars separately from app code
- It allows to externalize config (no hardcoding), share configs across multiple pods, update config without rebuilding image

- Suppose our app connects to DB, Instead of hardcoding, we can put the values inside configMap and app reads them at runtime
- This makes container reusable across envs where only configMap changes, not whole config of pod

- Define configMap - Use as env vars or as mounted files

<img width="704" height="245" alt="image" src="https://github.com/user-attachments/assets/3c8b5cfc-d0d9-4bbb-90e1-8fc3bc29716f" />

<img width="800" height="582" alt="image" src="https://github.com/user-attachments/assets/bb91d70e-5874-4ba4-8e5e-94fe70135336" />

<img width="790" height="670" alt="image" src="https://github.com/user-attachments/assets/4b8b930f-727b-495e-a4b2-18f893cd55b9" />

--------------------------------------------
