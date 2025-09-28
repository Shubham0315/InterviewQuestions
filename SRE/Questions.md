# Site Reliability Engineering Principles and work domain questions

How do you define Site Reliability Engineering (SRE) and how does it differ from traditional operations?
-
- SRE is about applying software engineering principles into operations and infrastructure problems. Its goal is to make systems more reliable, scalable and efficient by reducing manual work and automating processes
- Traditional operations teams often focus on maintaining systems reactively by fixing issues when arise and perform manual task. SRE on the other side treats operations as a software problem. Using SRE we can build tools, automation proactively to prevent issues, enforce reliability through SLOs, SLIs and create systems that are fault tolerant by design
- In my current role, instead of manually provisioning AWS infra, I automate it with terraform and have reduced setup time by 70%
- Similarly I implemented observability with Datadog and splunk which led us catch and fix recurring issues/outages proactively

------------------------------------------------------

Can you explain what "toil" means in SRE and how you’ve reduced it in your projects?
-
- Toil refers to repetitive, manual and operational work that doesnt add long term value but its necessary to keep systems running
- Like manual server setups, repretitive deployments or constant resolution of similar incidents
- In my current project I've reduced toil by automating infrastructure provisioning with Terraform to cut manual setup time by 70%
- Also created jenkins, GitLab pipelines which does automated builds, test, deployments reducing manual intervention during releases by 50%
- I've also implemented monitoring with DataDog and splunk which helped eliminate recurring outages and reduce time spent on reactive troubleshooting

------------------------------------------------------

What are SLOs, SLIs, and SLAs? Can you share an example of how you implemented or monitored them?
-
- These concepts in SRE are for measuring and managing reliability
- SLI - Service Level Indicator. A metric that measures specific aspect of service's performance like availability, latency or error rate
- SLO - Service Level Objective. The target or threshold for SLI such as 99.9% uptime or 200 ms response time
- SLA - Service Level Agreement. A formal contract with customers that defines expected reliability and usually includes panelties if targets are not met

- In one of my projects at Infosys, we defined an SLI as API availability and response time. The SLO was set to 99.9% uptime with less than 250ms latency. We implemented monitoring using Datadog and CloudWatch dashboards along with alerting on error rates and latency breaches. This gave us early warning signals and helped us address issues before they escalated into SLA violations for our clients.

------------------------------------------------------

How do you approach blameless postmortems? Can you walk through a real incident you handled?
-
- I see them as a way to learn from incidents without focussing on individual mistakes. The idea is to treat failures as opportunities to improve system and processes
- Its about how to prevent in future
- We always conduct blameless postmortem after issue to discuss the gap in implementation and lack of checks
- In our pipeline we have automated validation pipeline using CI/CD to test ingress rules before applying them. This ensures we eliminate similar outages going forward and improved overall deployment confidence.

------------------------------------------------------

What strategies do you use to balance feature delivery vs. system reliability?
-
- Its one of the challenges in SRE
- Define clear reliability targets like SLI, SLO, SLI. It provides a clear budget for allowable errors and downtime
- Allocate a percentage of allowable failures for new feature deployments
- Have incremental and safe deployments like canary, blue green, feature flags
- Use centralized logging, metrics and tracing for quick detection of anomalies. Setup alerts on key SLIs for proactive intervention. Here we can quickly rollback and mitigate issues without affecting reliability

------------------------------------------------------

How do you ensure your IaC templates (Terraform/CloudFormation) are secure and reusable?
-
- Break infrastructure into modules like VPC, EC2, EKS. Each module should be parameterized wit variables
- Use variables for env specific values
- Use module versioning to prevent breaking changes into prod. Maintain TF state securely with remote backends

------------------------------------------------------

Can you explain how you’ve set up highly available and fault-tolerant architectures in AWS?
-
- Used high level principles such as HA, fault tolerance and DR
- Core AWS strategies
  - Did multi AZ deployments
  - performed load balancing using ELB to distribute traffic across instances
  - dis auto scaling of EC2/EKS nodes
  - used RDS multi AZ for automated failover
  - stored static assets in S3 with cross region replication
  - Used EBS/EFS for backup and recovery
  - Deployed in VPC with public and private subnets across AZ
 
- For monitoring and logging used cloudwatch alarms with SNS notifications. Also used lambda for automated remediation

- Web application with below ensures no single point of failure, scales automatically :- 
  - ALB in front distributing traffic to EC2 in 2 AZ
  - EC2 auto scaling groups
  - RDS multi AZ for DB layer with automated failover
  - S3 + CloudFront for storing assets
  - Route 53 with health checks for DNS failover
  - CloudWatch + SNS for monitoring, alarms, and automated response.

------------------------------------------------------

What challenges have you faced working with EKS/Kubernetes in production, and how did you resolve them?
-
- Upgrading EKS versions without downtime was tricky as master was managed by AWS but workers needed manual upgrades. so we used node groups with rolling updates so pods can be scheduled gradually, also leveraged PDB and HPA to ensure app availability during upgrades
- For scaling apps due to sudden spikes we used HPA and CA for dynamic scaling
- Debugging issues in distributed systems was hard so we used datadog for metrics, logging and tracing. Defined SLI/SLO for latency, availability and error rates to proactively detect issues

------------------------------------------------------

How do you monitor cloud resources (EC2, S3, VPC, IAM, etc.) for reliability and compliance?
-
- To monitor reliability use cloudwatch metric, alarms, ASG. Check S3 events for S3 bucket update.  Use VPC flow logs to monitor traffic patterns and detect blocked traffic. Use NACL/SG.
- To monitor the compliance use AWS config to check if everything adheres to rules and regulations of the organization. Use centralized logging to store logs like S3. Use Config rules and lambda to auto fix non-compliant resources

------------------------------------------------------

What are some best practices for auto-remediation of incidents?
-
- Identify common, repeatable incidents suitable for automation like EC2 failure, unhealthy pods, low disk space
- Maintain reliable monitoring and alerting system like datadog. Define thresholds and triggers precisely
- Begin with low risk remediation tasks and gradually expand to more critical systems
- Implement validation and verification like check service health, confirm metrics response
- If remediation fails or causes issues, have rollback or fail safe mechanism
- Integrate with incident management so as to update tickets with JIRA

------------------------------------------------------

Walk me through how you troubleshoot a production issue when latency suddenly spikes.
-
- When latency spikes in production, I first confirm the issue via monitoring and alerts, then define the scope—identifying which services and regions are affected.
- I check recent deployments, review metrics, logs, and distributed traces to pinpoint the root cause—common culprits being resource saturation, network issues, or slow dependencies.
- I apply immediate remediation such as scaling resources, restarting pods, or rolling back changes, then verify system stability.
- Finally, I conduct a blameless postmortem to implement preventive measures and improve monitoring for the future

------------------------------------------------------

How do you use logs, metrics, and traces together for root cause analysis?
-
- Metrics help detect anomalies like spike or error rate and narrow down which service, region or instance is affected
- Logs are used to investigate specific errors or exceptions tied to metric. It identifies patterns or repeated failures. Logs provide timestamp and context
- Traces track request as it flows through distriuted systems or microservices. Identify latency or error

------------------------------------------------------

How do you secure communication between microservices in Kubernetes?
-
- Use K8S network policies to control traffic at pod level. Restrict which pods/service can communicate with each other. Ensures only authorized services can talk to each other
- Use SVC accounts and RBAC to control API access. Assign least privilege roles to pods that calls K8S API or other services
- Use ingress controller with TLS termination for external traffic, restrict egress to only required endpoints

------------------------------------------------------

How do you handle graceful termination of pods in Kubernetes?
-
- When pod is deleted or scaled down, K8S allows it to shut down cleanly rather than abruptly killing container to ensure resources are released properly and no data loss occurs
- When we initiate pod deletion K8S sends TERM signal to container process and pods enters into terminating state
- TerminationGracePeriodSeconds defines how long K8S wait before sending KILL signal, default is 30 sec
- If container does not exit within grace period, K8S sends SIGKILL to forcefully terminate it

- In Kubernetes, graceful termination ensures pods shut down cleanly without dropping in-flight requests. Kubernetes sends SIGTERM, waits for terminationGracePeriodSeconds, and then sends SIGKILL. I handle it using readiness probes to stop traffic, PreStop hooks to clean up resources, and SIGTERM-aware applications to finish ongoing work. This approach prevents downtime and ensures reliable pod shutdowns during scaling or upgrades

------------------------------------------------------

What’s your approach for handling database reliability and backups in an SRE role?
-
- Use multi AZ deployments for HA using RDS. Deploy replicas to distribute read traffic and reduce load om primary DB. Set alarms for anomalies like high latency, failed requests
- Enable RDS auto backups. Take manual snapshpots before upgrades. Replicate backups across regions to protect against regional outages

------------------------------------------------------

Can you give an example of a bash script you wrote for automation or monitoring?
-
- Disk usage monitoring script
- Automated log rotation and cleanup for servers :- find . -type f -mtime +7 -exec rm -f {} ;
- Log analysis and monitoring :- grep "ERROR" /var/log/app.log | awk '{print $1,$2,$5}' > error_report.txt

------------------------------------------------------

How do you structure error handling and logging in your scripts?
-
- Use exit on error and error codes. Check exit codes ($?) to detect failures. Use set-e in scripts to exit immediately if any command fails
- Include context, timestamps and failing step in error messages
- Separate log levels like INFO, WARNING, ERROR. Use log rotation strategy (/etc/logrotate.conf)
- Scripts should return proper exit codes 0,1,2,4

------------------------------------------------------

Explain a scenario where your scripting reduced manual effort significantly.
-
- Auto EC2 health check and restarts.
- If they become unhealthy due to resource usage or service crashes. Manual process of monitoring was time consuming and error prone.
- Use bash script - Pull EC2 metrics from CLOUDWATCH CLI - Indetify instances exceeding CPU or memory thresholds - Restart unhealthy instances automatically - Send alerts via slack or email

- I wrote a Bash script to automate EC2 health checks and restart instances when CPU exceeded a threshold. This eliminated manual monitoring, reduced incident response time, and ensured 24/7 reliability. It saved ~2 hours/day of manual work and made the system more scalable and resilient

------------------------------------------------------

Your production service is down. Walk me through your first 5 steps.
-
- Acknowledge the incident over alerts and inform stakeholders that its WIP
- Check which system is affected looking at dashboards and identify whether its total outage or partial degradation
- Rollback recent deployment if applicable or scale resources temporarily if traffic is more
- Check logs data, check change history like GIT/Pipeline/Configs and correlate with failure time
- If issue is beyond you scope, pull relevant teams and keep communicating status to stakeholders.Document steps taken to avoid in future

- Once service is restored perform RCA and blameless portmortem

------------------------------------------------------

Latency suddenly spikes in one of your critical APIs — how do you troubleshoot?
-
- Check dashboards like latency, error rates
- Correlate with recent changes. Ceck resources
- To mitigate rollback the change, scale pods/instances or reroute traffic

------------------------------------------------------

You receive multiple alerts at once from different services — how do you prioritize?
-
- Identify business impact which is customer facing or revenue critical systems
- Classify severity based on outages or degradation and other types
- Mitigate critical issues first then address lower priority issues
- Keep stakeholders updated on status of issue

------------------------------------------------------

A deployment just caused an outage — how do you roll back and prevent this in the future?
-
- Verify outage is linked to recent deployment and using blue-green, canary or kubectl rollout undo rollback it to restore latest stable version. Check service health returns to normal
- To prevent future outages
  - Add stronger CICD gates like security scans
  - Use canary for gradual rollouts
  - Track key metrics before user traffic begins
  - Document incidents and update runbooks
 
- If a deployment causes an outage, I immediately roll back to the last stable version and monitor system recovery. To prevent recurrence, I’d enforce automated tests, adopt safer rollout strategies like canary or blue-green, improve monitoring during deployments, and run a blameless postmortem to capture learnings

------------------------------------------------------

You’re getting too many false alerts from your monitoring system. What steps would you take to tune it?
-
- Analyze them and confirm if they're actionable or just noise. If CPU alert triggers during backups but service health is unaffected, thats a false alert
- Adjust thresholds
- Introduce multi signal alerts by combining CPU, pods restarts, error rate
- Set clear severity levels
- Every alert should map to clear documentation. If X fires, use Y

------------------------------------------------------

A dashboard shows CPU and memory usage are normal, but users still report slowness — what’s your approach?
-
- Reproduce user issue checking app response times, API latency, error rates
- Check for app metrics like request/response latency, error rates like 400/500
- Check DB, caches, MQs. Slow DB may bottleneck app
- Look at network I/O as latency could come from high disk I/O wait times, LB misconfigurations
- Review recent changes like deployment, configs, infra update
- Mitigate while investigating like scale instances or pods

------------------------------------------------------

Logs are growing too fast and filling up disk space — how do you handle log rotation and retention?
-
- Configure log rotation strategy using logrotate to compress, archive logs. Stores at /etc/logrotate.conf
- Also set retention policies and retention service. 7 days at S3
- This ensures disk space is safe while maintaining compliance

------------------------------------------------------

An EC2 instance in production is unreachable — what checks do you perform?
-
- Check if instance is running, stopped, terminated
- Check network connectivity using ping, telnet to IP. Check SG/NACL for allowed traffic
- Check SSH access for instance using correct creds of key-value pair
- Check CPU, memory, disk, process hangs
- Check recent deployments, changes
- Reoot instance if its unresponsive but all checks are OK

------------------------------------------------------

One of your S3 buckets accidentally exposed data publicly — what immediate and long-term actions do you take?
-
- Immediate actions to restrict access like S3 policy, block public access.
- For long term enforce least privilege for all buckets, remove public ACLs unless strictly needed. Enable S3 server access logging, setup cloudwatch alerts for public bucket exposures. Automate compliance checks using config

------------------------------------------------------

A region in AWS goes down — how do you design your systems for high availability and fault tolerance?
-
- Deploy services across atleast 2 regions
- Spread instances across multiple AZs to handle single AZ failures
- Use cross replication for S3 and DBs. Automate regular snapshots
- Design stateless services.
- Automate deployments with terraform and setup monitoring

------------------------------------------------------

Your Terraform apply fails due to drift — how do you resolve it?
-
- Identify drift using plan command to see which resources have diverged from scripts
- Assess impact of drift if its critical or minor
- Correct the drift using apply to bring resources back in sync with config
- To prevent future drift, enforce IAC only changes avoiding manual soncole changes

------------------------------------------------------

A stateful set in Kubernetes is losing data after pod restarts — what’s your approach?
-
- First check if PVCs are correctly attached and backed by durable storage
- Verify storage class supports persistence across pod restarts like EFS
- Ensure app writes data to mounted volumes rather than ephemeral pod storage

------------------------------------------------------

DNS resolution is failing inside Kubernetes pods — how do you troubleshoot?
-
- Verify /etc/resolv.conf inside pod and ensure pod's DNS policy is correct
- Test DNS resolution
- Verify Core DNS pods running and healthy
- Ensure connectivity if pods can reach DNS service IP
- Check for network policies that might block DNS traffic

------------------------------------------------------

You notice engineers often forget to rotate secrets — how would you automate this?
-
- Use secret manage tool lile secrets manager or vault
- Configure auto rotation policies for credentials
- Ensure app fetch secrets dynamically at runtime instead of hardcoding
- Setup alerts if secrets like cert are nearing expiration or fail rotation

------------------------------------------------------

You’re asked to reduce mean time to recovery (MTTR). What automations would you introduce?
-
- Detect common failures and use remediation scripts for auto service restart, scale pods
- Implement proactive health checks and alerts to catch issues before issues report them
- Use K8S proes for auto restart pods on failure and auto replace unhealthy instances
- Automate routine remediation tasks documented in runbooks via scripts

------------------------------------------------------

How do you convince product managers to respect error budgets when they push for fast feature delivery?
-
- Show how exceeding error budget directly impacts customer experience and revenue
- Use metrics from SLO/SLI dashboards to illustrate current reliability and risk
- Involve PMs in planning, allocate remaining error budger for new features. Suggest incremental rollouts to release safely while respecting budgets
- Show historical incident trends and potential downtime if error budgets are ignored

------------------------------------------------------

Developers push new features frequently, and reliability is suffering. How do you handle the tradeoff?
-
- Use SLOs, SLIs, and error budgets to quantify how frequent releases impact reliability.
- Use canary releases, feature flags, or blue-green deployments to minimize impact of new features
- Integrate automated testing, CI/CD pipelines, and proactive monitoring to catch issues early.
- After incidents, conduct blameless postmortems and improve processes.

------------------------------------------------------
