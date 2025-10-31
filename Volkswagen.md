# ✅ L1 Volkswagen Technical ( Questions) 36 Minutes

How is your experience with agile and ITIL frameworks? Have you got a chance to work with priority incidents?
-
- Frequent and general question
- Working with incidents and ITIL/ITSM process.
- Ticketing tools like cherwell and remedy

-----------------------------------

You notice some intermittent performance issues with DB hosted on EC2 and its using EBS storage. How will you investigate and troubleshoot these issues?
-
- We need to check this issue at 3 levels - instance, storage and DB

- **Instance level checks**
  - Use datadog metrics to check CPU, memory and network utilization
  - Verify EC2 type has sufficient capacity for the workload
 
- **EBS level checks**
  - Use datadog to monitor EBS volume latency, IOPS
  - Identify if its hitting EBS IO limits. Then we can opt for high performance volume type like gp3 or io2
 
- **DB level checks**
  - Analyze slow query logs, connections
 
- For remediation, scale DB vertically or horizontally. Enable enhanced monitoring and performance insights.
- Consider moving to amazon RDS for managed performance optimization

-----------------------------------

Can you explain difference between cloudwatch logs and cloudwwatch metrics?
-
- **Metrics**
  - CloudWatch Metrics show quantitative performance data like CPU or memory
  - Its for numerical monitoring
  - If any metrics is 85% we can set alarms for it. We can plot metrics on dashboard to monitor performance trends
 
- **Log groups**
  - CloudWatch Logs store qualitative event details from applications and systems
  - Captures raw application/system logs that explain why something went wrong.
  - Its like console output of jenkins for each run
 
-----------------------------------

How do you approach SLA reporting and analysis to stakeholders?
-
- My approach to SLA reporting and analysis follows a structured process that covers data collection, validation, visualization, and communication.

- **Define SLA and key metrics**
  - Work wit teams to define SLA parameters depending on business and customer
  - 99.9% uptime, <=200ms response times, incident response time <= 15 mins, MTTR <= 30 mins, deployment success rate > 98%
  - They are mapped to SLO and SLI
 
- **Data collection and monitoring**
  - I ensure every SLA-related metric is continuously measured and logged using monitoring tools like datadog
  - It measures CPU, latency, errors, uptime,
 
- **Visualization and reporting**
  - Once collected data is analysed, I prepeare visual reports and communicate the same with stakeholders
 
- **Continuous Improvement**
  - I conduct post-incident reviews (PIRs) for SLA breaches so Root cause is identified, actions are taken to prevent in future and lessons can be documented
 
-----------------------------------

Can you explain scenario where your reports facilitated any strategic decision or process improvement when you reported SLA?
-
- In one of my previous projects, I was responsible for SLA reporting for application uptime and incident resolution in a production environment hosted on AWS EKS
- Over several months of SLA reports, I noticed a pattern — our uptime was dipping below 99.9% mainly during release windows and scaling events.

- I correlated datadog with deployment timestamps from Jenkins pipelines. This analysis revealed that most incidents occurred due to pod restarts caused by resource throttling and manual deployment errors.
  - I presented this trend in the monthly SLA report to management, with visual dashboards from datadog highlighting downtime patterns.
  - Based on this I implemented HPA to handle load spikes, adding probes in Kubernetes manifests to prevent traffic from hitting unhealthy pods
 
- These changes reduced deployment-related downtime by nearly 60% and helped us consistently maintain 99.95% uptime over the next two quarters.

-----------------------------------

There is performance issue in business critical application which seems to be related to DB query from logs. How would you handle the situation minimizing impact and later resolve it?
-
- First Detect and Acknowledge the Issue as it will be P1 due to business critical app. Inform stakeholders and start warroom
- Try to stabilize system quickly minimizing customer impact. Temporarily scale resources, restart slow queries
- Once system is stable, start finding root cause
  - Check app logs to identify queries
  - Common causes can be invalid indexes, poor JSON, connection pool exhaustion, sudden traffic surge
 
- To fix permanently
  - Add proper indexes
  - Tune DB parameters like max_connections, review autoscaling config
  - If on EC2, consider moving DB to RDS for managed performance features.
 
- Then conduct a PIR

-----------------------------------

Your monitoring system give alert of high CPU usage unexpectedly on K8S node. How would you troubleshoot to identify cause and mitigate it?
-
- Check the timestamp and trend of CPU spike in the monitoring dashboard. Identify which node and which pods are consuming resources.
  - Commands :- **kubectl top nodes**
 
- Use describe pods command to deep dive. Look for high CPU or restarts due to resource throttling.
- Check resource requests/limits. Sometimes CPU spikes happen because resource requests and limits are misconfigured — pods consuming more CPU than allocated.

- To minimize impact
  - Cordon the node - prevent new pods from scheduling
  - Drain the node if necessary and reschedule pods elsewhere:
  - Restart the problematic pod or service if it’s stuck in high CPU usage loop.
 
- Long term fix
  - Right size CPU requests/limits
  - Implement HPA, cluster autoscalar
 
-----------------------------------

Can you explain what is toil and how can we reduce it?
-
- Toil is the manual, repetitive, and automatable work that keeps your systems running but doesn’t add long-term value.

- To reduce toil
  - automate repetitive tasks like terraform and scripts of shell
  - self healing and automation
  - optimize monitoring and alerting
  - CICD pipelines
 
-----------------------------------

Have you got chance to work on budget policy?
-
- I’ve created AWS Budgets to monitor monthly spend at the account or service level and configured budget alerts via SNS and email to notify when usage crossed a certain threshold.
- These policies helped the team stay within budget and identify unused or underutilized resources for optimization.

-----------------------------------

Can you explain some agile metrics and why we need to focus on them?
-
- Agile metrics are measurements that help teams track progress, quality, and efficiency of software delivery. They show how effectively a team is delivering value to customers, identify bottlenecks, and support data-driven decisions for improvement.

- Velocity :- The total amount of work (usually in story points) completed in a sprint.
- Sprint burndown chart :- A visual graph showing remaining work vs. time in a sprint.
- Lead time :- Time from when a task is created to when it’s delivered to production.
- Cycle time :- Time taken from when work starts to when it’s completed.
- Deployment frequency :- How often code changes are deployed to production.
- Change failure rates :- Percentage of deployments causing production issues or rollbacks.
- MTTR :- Average time to recover from a production incident.

-----------------------------------

In terms of team management, your team members are cherry picking tasks within team. Person is working on single type of tasks. How will you manage?
-
- Understand root cause
  - Is the person more comfortable with certain tasks due to skill gaps?
  - Is there lack of clarity in sprint goals or ownership
 
- In sprint planning, I’d make sure task distribution happens collaboratively with clear ownership.
- Every team member should take diverse tasks — not just what they’re best at — so the whole team builds cross-functional capability.

- I would encourage knowledge sharing and then rotating ownerships to build confidence and collaboration within the team

-----------------------------------

What is service lifecycle model in ITIL?
-
- The ITIL Service Lifecycle is a framework that defines how IT services are designed, delivered, managed, and improved throughout their life.
- It ensures IT services are aligned with business needs and provide consistent value over time.

- Ensures IT services deliver measurable business value.
- Creates a structured approach for designing, deploying, and improving IT systems.
- Improves customer satisfaction and operational efficiency.
- Integrates naturally with DevOps practices, which focus on continuous delivery and improvement.
