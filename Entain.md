# ✅ Entain Technical SRE ( Questions) Minutes

What is the problem you solved in your recent past?
-
- Migration of deployments from on-premises to EKS clusters. There we discussed which monitoring tool we can integrate with our EKS cluster to get maximum insights with alerting and logging mechanism
- We considered cloudwatch, splunk, datadog.
- We choose datadog deploying it as a daemon set on all our cluster nodes

------------------------------------

What was your evaluation criteria to choose monitoring tool for your EKS cluster?
-
- We needed all things for our monitoring tool like metrics, logging, tracing, alerting, monitoring dashboards
- Our criteria was
  - It should have native support to our K8S objects with easy integration along EKS
  - Ability to capture infrastructure metrics like CPU, memory, disk, network as well as app level metrics like API latency, request errors
  - Centralized logging
  - Tool should handle large scale workloads with 100s of pods and auto scaling clusters
  - Real time dashboards visualization
  - Custom alerts with integrating slack, mail, teams
  - Security and compliance like RBAC, data encryption
  - Cost optimization with feature richness
 
------------------------------------

What are the most important metrics you consider while monitoring EKS cluster using datadog?
-
- Node metrics like CPU utilization, memory, disk, network I/O.
- No of nodes ready vs not ready to accept traffic or scheduling of pods
- Pod status , container restart, request vs limits to control provisioning of resources
- API server request latency and error rates, scheduler performance, etcd health
- Service call rates per endpoint, 400, 500 errors like unauthorized access attempts, network policy violations

------------------------------------

Can you explain the joruney of migrating from on-prem K8S cluster to EKS? What was the approach that was considered moving to EKS cluster? What inputs you needed from DEV team? What were the automations you did from SRE perspective?
-
- Migration
  - Evaluated on-prem things like cluster size, workloads, networking, storage of cluster. Identified dependencies
  - Chose migration approach - critical service first and then non-critical ones. Defined networking model using VPC, NACL, SG, subnets. Selected monitoring tools. Planned IAM roles, RBAC, secrets management
  - Seup EKS with terraform. Migrated workloads in different namespaces. Validated app in test env first
  - Performed blue-green deployments and canary release for app to minimize downtime. Decommissioned on-prem workloads once app was stable on EKS
 
- DEV team help
  - App dependencies like DB, configs
  - Resource requirements like CPU, memory, storage
  - Env vars and secrets
  - CICD integration to build artifacts/images, deployment strategy
  - Scaling requirements like HPA threshold, concurrency limits
 
- SRE Automation
  - Used terraform to create resources on AWS like VPC, Subnets, EC2, EKS cluster
  - Automated Jenkins CICD deployments on EKS
  - Monitoring and alerting using datadog dashboards/alerts
  - Automated IAM roles fpr Service acc and secret injection
 
- No need of SonarQube to scan images as from ECR only scanned images were processed
- This was done to have HA, node auto scaling, security aspects, vulnerability free images
 
------------------------------------

Was the above migration done with downtime?
-
- We first deployed workloads on EKS in a phased manner. Critical workloads were deployed first and then non-critical.
- Kept on-prem and EKS running in parallel using DNS based traffic routing to control rollout
- Once the EKS cluster was stable, we decommissioned the on-premises cluster

------------------------------------

Its quite difficult to manage manifest files for different services or files like deployments, HPA, secrets, services, ingress. How are you managing them? Did you evaluate any other tools to manage manifest files?
- 
- Yes, managing raw Kubernetes manifests (deployments, HPA, secrets, services, ingress) at scale quickly becomes complex, especially when dealing with multiple microservices, environments, and configuration variations
- But for services under a single app you can say files will be mostly identical in structure, just need to make changes for some service related aspects like image name, service type, port to expose and all
- We can actually use helm charts or GitOps tools like ArgoCD for the same. Helm is for complex tempating and chart reusability

- Suppose 2 services are linked to each other like inbound and outbound. There we need to define ingress and egress rules like to, from, port, protocol, CIDR. We can use same content just by changing required parameters

------------------------------------

