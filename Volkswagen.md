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
 
- Visualization and reporting
  - Once collected data is analysed, I prepeare visual reports and communicate the same with stakeholders
 
- Continuous Improvement
  - I conduct post-incident reviews (PIRs) for SLA breaches so Root cause is identified, actions are taken to prevent in future and lessons can be documented
 
-----------------------------------

