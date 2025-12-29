# ✅ L1 Entain Technical Assessment SRE

# ✅ L2 Entain Technical SRE (13 Questions) 62 Minutes

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

Scenario based on dockerfile provided
-
- Python service to be dockerized needs to do a code review of dockerfile
- Check dockerfile and suggest if anything needs to be changed
- The dockerfile size is very huge. So we can use multi stage builds to separate the build and runtime. Only strong base image we can use in 1st stage and copy required artifacts to next stage with minimal dependencies and pass the CMD/ENTRYPOINT in last stage for the runtime

------------------------------------

When to use blue green and when to use canary deployments?
-
- In blue green we maintain 2 environments. We can switch the traffic between 2. Once new version on green is validated then we can switch traffic from blue to green. If issue occur, rollback can be done intsnantly
- We can use it for large released, critical apps, fast rollback cases

- In canary we can gradually rollout new version to small subset of users first. Monitor performance, errors and metrics. Then slowly increase traffic until 100% runs on new version.
- Rollback can be done by reducing canary traffic back to stable version
- Used in frequent releases, non critical apps

------------------------------------

How do you justify the cost of maintaining 2 environments in blue green deployments? What factors are considered while applying this strategy?
-
- Cost Justification
  - Even few mins of downtime can make huge revenue impact
  - Rollback from failed release is just a traffic switch
  - Testing in prod env replica so actual testing is done
 
- Factors considered
  - Business criticality
  - Release frequency
  - Traffic pattern
 
------------------------------------

When you are trying to spinup 10 ECR machines using terraform, 5 gets created and at the time of creation of 6th the node goes down on EKS. State file is stored at S3. How would you recover from this error of stale state?
-
- State file in S3 may not match actual infra. Some resources exist in AWS but TF may think they dont

- First stop further TF runs and check cluster/node health
- Check TF logs, inspect remote state objects and versions
- Decide strategy to restore state
- Check for the created resources first :- **terraform state list**
- Refresh local view of TF resources :- **terraform refresh**
- Import existing resources in state file :- **terraform import**

- If EKS goes down, check ASG and let it recreate instance or manually trigger replacement

------------------------------------

We're running a Job is CI pipeline. One of the API call is getting timed out which DEV is also unable to figure out. How would you help them to figure this out?
-
- Reproduce issue locally by running same API call from CI runner and check if timeout happens. If its only in CI then likely network/firewall, DNS or proxy issue
- Check connectivity like firewall, SG, NACL
- Check in which env job is running exec into pod
- Ask DEV team to enable API side logging as well. Check API latency dashboard, app logs, response times metrics
- Check pinging on that API endpoint if its reachable, check network statistics.
- Try curling into endpoint if its in external world

------------------------------------

What kind of incident managemnt tools you have used and what activities done?
-
- Ticketing,change tools used like JIRA, Cherwell
- Worked in Release managemnt team where we needed to prepeare release documents and share with change management along with artifacts
- RFC documentation preparation like Impact sheet, relase notes with jar file components
- Worked on sev2, sev3

------------------------------------

What you do usually when you want help from other teams and they're not responding? How leadership address these kind of things?
-
- We always have syncup call with our client manager where we highlight pending issues
- All client managers also have a call twice a week where they discuss if any dependency is there from other teams and help is needed. So we get our SPOC is any issue is pending since long
- Also we have SLA of 72 hrs to resolve issue. So we try to get incident resolved within the timeline

------------------------------------

✅ L3 Entain Technical SRE - 58 Minutes

- Given a payment application architecture diagram and was asked to elaborate the same for e-commerce application. 
- Framed some questions based on my responses based on monitoring and observability using DataDog and Cloudwatch with ITSM/ITIL processes
