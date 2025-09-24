# ✅ Fortinet L1 Technical ( 27 Questions) 58 Minutes

Explain the architecture of your project which is deployed on EKS/K8S cluster
-
- We're jsing AWS EKS to manage K8S clusters which provides control plane components like API server, scheduler, etcd while we're managing our worker nodes using EC2 and fargate
- Cluster is setup in such a way that VPC is there with private/public subnets. Worker nodes are deployed in private subnets which actually runs apps. Public subnets hosts ALBs for ingress traffic
- Application is containerized using docker. For each microservice we're creating dockerfile running it as K8S deployment with multiple replicas. We're using cluster IP for inter service communication and LB/ingress for external traffic
- We're having ALB ingress controller managing routing of traffic to services. Ingress defines our path/host based routing domains
- For ststeful components we're using PV/PVC with EBS. EFS is also used if shared storage is required
- We're also using CM and secrets to store values, integrating with AWS secrets manager
- We're using IRSA so pods can securely access AWS services. Define network policies, enforcing RBAC
- In our CI/CD pipeline stages are code - docker build - push image to ECR - Deploy to EKS using kubectl
- For observaility we're using splunk and datadog for cluster and node/pod level monitoring
- For scalability we're using HPA which scales pods based on CPU/memory. Cluster autoscalar to scale worker nodes
- We're also using PDBs and probes to ensure zero downtime deployments

<img width="719" height="158" alt="image" src="https://github.com/user-attachments/assets/f56e2d99-a0c5-43b6-a494-a456b6a762dd" />

-----------------------------------------------

When you deploy your app on worker nodes, does your replication use any external services like message queues and how do you manage them?
-
- Yes in many microservices based system, we're using MQs for communication between services
- In EKS we're managing them by using SQS. They're external to EKS cluster but integrated with workloads via IRSA so pods can access them
- Replication at app layer is handled by K8S deployments having multiple replicas distributed across nodes. For MQs, SQS we're using

-----------------------------------------------

Can you explain monitoring and logging used for your deployment?
-
- We've datadog agent deployed on all worker nodes as daemon set
- It ensures every node runs pods and collect metrics, collect logs, send data to datadog SaaS platform
- Our K8S cluster is integrated with datadog which collects node utilization, resource usage metrics which we expose
- While doing monitoring, to collect or check logs we're using trace IDs to find the exact count or time of error and drill down to check bottlenecks, error tracking within specific timespan
- We also have custom dashboards for node health, pod counts, KPIs like service call rates, request counts
- For alerting we've set alerts on our webex spaces if pod crash loops, restarts, node not ready for traffic, resource saturation, high latency in services, etc

- We're also utilising splunk queries to find out the common error is any

-----------------------------------------------

If any scaling is required for the deployment you've created, how you can do it?
-
- For pod level scaling we use HPA for deployments using which we scale no of pods/replicas based on CPU/memory utilization, custom metrics like request latency, queue depth
- We also use VPC for workloads that need more CPU/RAM per pod to adjust pod resource requets/limits automatically
- If cluster dont have enough worker nodes, pods remain pending state. So we use cluster autoscalar which auto adds/emoves nodes in node group (EC2 auto scaling groups) when pods cannot be scheduled
- To scale manually :- **kubectl scale deployment patient-service --replicas=5**

-----------------------------------------------

Have you used HPA in your current project? What kind of conditions you're setting in HPA manifest files?
-
- Yes. Since our app is mostly user facing with variable workloads deploying multiple services, we configure HPA based on CPU utilization and custom metrics like memory, request count, etc.
- If our CPU usage crosses 70%, our services scale according to the condition we set in our HPA and deployment manifest files
- In deployment file, we define min request and max limit for metrics based on which we need to scale resources
- If we're scaling pods based on CPU, in HPA file mention min and max replicas or pod, what is the resource and under what condition scaling is to be done.
- This ensures we can handle peak traffic without over provisioning while scaling down during low usage hours to save cost

-----------------------------------------------

How are you maintaining High Availability if you have 70 pods running on multiple nodes?
-
- We're usually managing pods using Deployments or Stateful Sets. Replica sets are ensuring desired replicas are always running. If we've 70 pods which are spread across nodes with 2-3 replicas per pod. So even if one pod fails, RS auto spins up replacement on healthy node
- Also we're using node affinity rules to spread pods across multiple nodes or zones. This ensures even if one node goes down, only subset of pods is affected
- Using health checks like liveness, readiness probes
- We're using HPA as well for scaling. Using service traffic is distributed across healthy pods
- We can have multiple master nodes and etcd cluster to maintain HA

-----------------------------------------------

How do you define there will be 3 master nodes or multiple etcd instances for app?
-
- Master node and etcd are brain and DB for our K8S cluster
- So in K8S setup, we need multiple masters in odd numbers to ensure consensus. In cloud K8S clusters, automatically multiple masters are used across AZs
- Etcd is backing store that should also be in odd
- This ensures cluster to continue functioning even if any node fails

-----------------------------------------------

How much traffic is required to have 20 master nodes?
-
- First we need to monitor "apiserver_request_total", request latencies, watch counts. If we see rising latencies or etcd write latency, then scale API server replicas or scale control plane VMs
- If we're hitting extreme traffic then consider API shrading

-----------------------------------------------

If we need pods to run on multiple nodes, how to achieve that?
-
- Use pod anti affinity so pods of same app will be distributed across nodes. No 2 pods will be on same node
- Use replicas across nodes. K8S auto does that as scheduler tries to balance pods
- We can also use daemon sets so one pod will be there per node

<img width="656" height="265" alt="image" src="https://github.com/user-attachments/assets/92eb6645-f2df-4e52-8814-b2e032dc9d5f" />

-----------------------------------------------

Using anti affinity, how to manage 10000 pods?
-
- Here using anti affinity can cause high scheduling latency as scheduler has to check every new pods against all existing pods
- At that scale, we typically use topology spread constraints or preferred anti-affinity instead of hard requirements.
- If we need strict one-pod-per-node behavior, we use DaemonSets.
- For very large clusters, we may shard workloads across multiple clusters to avoid scheduler bottleneck

-----------------------------------------------

If there are multiple worker nodes and 100s of pods are running on them, one node goes down due to some reason, what will happen in that case? How they will be scheduled on other nodes?
-
- Pods on that node will be unreachable, unknown state
- After grace period of 5 mins, node controller evicts pods from that node and create replicas on healthy nodes
- LB or Kube proxy auto stops traffic to those pods and route to healthier ones

-----------------------------------------------

If any node is down on prod how will you get to know that? What will be the troubleshooting steps?
-
- We can check manually by "kubectl get nodes" which will show not ready, scheduling disabled
- We can also set cloudwatch alarms like if "node_status_condition" is false.
- Node CPU/memory usage spike before failure so we can have idea in monitoring

- To troubleshoot :-
  - Confirm node status
  - Check pod impact if they're rescheduled to other nodes
  - Investigate node by doing SSH, systemctl status kubelet, df -h, top, ping
  - Check its logs
  - If temporary crash is there, restart services
 
-----------------------------------------------

If we're not using cloudwatch, what could be other way to check if node is down in automatic way? Where the cron job will run
-
- As in our project we're using datadog, it auto discovers nodes and pods. We ave setup alerts using it which comes on our mails as well as webex spaces. So even if any node variable is changed, it detects and reports
- Using cronjobs as well we can set this alerts
- Command :- **kubectl get nodes --no-headers | grep -v "Ready" | awk '{print $1}'**

<img width="744" height="140" alt="image" src="https://github.com/user-attachments/assets/f4cbedb0-897d-478c-a16c-22dbe0273fbe" />

- We can set cron inside K8S cluster as cronJob resource
- Location :- crontab -e, crontab -l 

-----------------------------------------------

How cloudwatch detects things like node is down, crashLoopBackoff errors?
-
- We can integrate cloudwatch with K8S using 2 components
- Cloudwatch container insights :- its deployed as daemon sets on all nodes which collects metrics from runtime, nodes, pods and sends mlogs to cloudwatch metrics
- Node down error is when kubelet stops sending metrics/heartbeats. We can create alarms if nodeStatus == 0 for X minutes, trigger alarm
- To detect crashLoopBackoff, check pod-level metrics like PodRestartCount, pod status and set alarm accordingly

-----------------------------------------------

Can you tell me the process of deployment of app on K8S cluster?
-
- Prepare app :- Package in container image using dockerfile
- Define K8S manifests like deployments, service
- Apply manifests to cluster
- Verify deployment and logs, status
- Expose app using services as LB or nodePort. Map domain to ingress controller using ingress
- For update/rollout use rolling update or any strategy

- Others :- Pod anti affinity, Probes, HPA, PV, monitoring and logging

-----------------------------------------------

How do you update the application or image, how to do that?
-
- Update image in deployment manifest and apply changes
- Using rolling update :- **kubectl rollout status deployment/my-app**
- Using set image to change image of containers and perform rollout command then

-----------------------------------------------

If there are 100s of microservices running, how can we change image of each?
-
- Not feasible manually
- Use CICD pipelines. Each microservice has dockerfile and K8S manifests. On image change CI builds new docker image pushing to registry and then CD updates K8S manifests with new image deploying on K8S auto
- Also we can use automation scripts

-----------------------------------------------

How do you update the config map if the CM value is changed?
-
- Pods dont auto restart when CM changes so values updated will not be reflected
- First create CM. If value changes, edit CM and apply
- To make pods pick up new config, mount CM as volume so changes are reflected inside container automatically
- Also we can use env vars "envFrom"

-----------------------------------------------

What is your update strategy for prod clusters like EKS?
-
- Updating prod K8S neds to ensure HA, zero downtime, minimal risks
- If we're using EKS, AWS managed our EKS and worker nodes auto. We still need to monitor versions and decide when to upgrade worker nodes
- First we backup etcd and critical app manifests and PV snapshots
- Update worker nodes, drain them first gracefully and apply rolling update
- Then do monitoring

-----------------------------------------------

How do you ensure there is zero downtime during app upgrade?
-
- Use rolling updateas K8S gradually terminates old pods while starting new pods with updated image
- Use readiness probes to ensure new pods dont receive traffic until they're fully ready
- Use LB/service which routes traffic only to pods that are ready. Combine with rolling update and probes
- Use blue green deployments
- Use canary deployment
- Use PDB

-----------------------------------------------

How big is your prod cluster? How many master and worker nodes are there?
-
- In our production EKS cluster, the control plane is managed by AWS across 3 availability zones for high availability. We run multiple managed node groups with a total of around 10–15 worker nodes. Each node runs 20–30 pods, and the total number of pods in the cluster is around 200–300. We use horizontal pod autoscaling to handle peak traffic

-----------------------------------------------

How many users consume these services?
-
- Around 1 million US users. Explain service rates of monitoring

-----------------------------------------------

For each service how many replicas are there?
- 
- Frontend :- 3-5 replicas
- Microservices :- 2-3 replicas
- DB :- 2
- All across AZs

-----------------------------------------------

How do you maintain security of cluster to ensure its vulnerability free?
-
- In EKS, we use RBAC to limit access, enable IAM roles , restrict VPC endpoints
- For node security we use latest AMIs, limit SSH access, IAM administration, enable auto patching
- Separate workloads using namespaces. Assign least privilege RBAC roles per NS
- Use secrets management tools like AWS and TF vault
- Use scanned images integrating with SQ scanners

-----------------------------------------------

How are you managing secrets of applications?
-
- AWS scerets manager (parameter store) and TF vault
- Using vaults we're mounting secrets on pods/nodes using volumes

-----------------------------------------------

Who is taking deciions of architecturing your app?
-

-----------------------------------------------

What is the architecture of your on-prem applications deployments?
-

-----------------------------------------------


