# ✅ Fortinet L1 Technical ( Questions)  Minutes

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

