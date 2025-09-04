# Aligned Automation

From K8S perspective what standards do you follow for the on-premises deployments?
- 
- Set up HA K8S cluster (multiple master nodes). Have periodic etcd snapshots, multiple replicas
- Enable RBAC across all namepsaces on cluster. Secrets management  using K8S secrets or vaults or AWS Secrets manager
- Have service exposure standards like clusterIP for internal communication, LoadBalancer for external
- Define CPU and memory requests/limits for every pod. Use namespaces for logical isolation between env. 
- Use monitoring tools like prometheus for metrics collection, EKS for logging and use liveness/readiness probes for health checks for all pods
- Define proper storageClasses for dynamic provisioning
- Use CICD tools 
- Drain nodes gracefully, use rolling updates and pod disruption budgets
- Set Backup/restore policies for etcd and persistent volumes.

- Write yml files for resources needs to be created and set standards

---------------------------------------------------------------------------------------

How to use Hashicorp vault in CICD pipeline?
-
- Using vault in CICD pipeline is a secure and scalable way to manage secrets without hardcoding them in our pipeline code or config files
- Vault has secret engine enabled (KV - Key Value), policy and authentication methods
- Setup vault and authenticate pipeline via vault :- Enable auth method as "app role"
- Store secrets in vault
- Integrate vault with CI/CD tool. First install vault plugin in jenkins, configure vault server in jenkins by providing vault address and credentials in creds manager
- Use secrets in Build/deploy. So secrets are injected at runtime, never stored in repo.

- create policy, write policy and generate role, get role id and secrets ID, authenticate in pipeline
- Retrieve secrets in pipeline
- Use in CICD tools

- Create KV in vault and use in CICD pipeline

---------------------------------------------------------------------------------------

How to setting up splunk for any application?
-
- It involves configuring log forwarding, indexing and visualization so that we can collect, monitor and analyze logs in real time
- Install splunk enterprise for on-prem ir provision splunk cloud
- Configure index for application :- **index=app_logs**
- Decide what to log :- Access logs, error logs, custom app logs, security events, audit logs :- /var/log/myapp/app.log
- Splunk doesnt directly pull logs, it need forwarder. So, Install splunk universal forwarder on app server
- Define source types how to parse logs (JSON, CSV, syslog)

- We can also enable HEC (HTTP Event Collector) for kubernetes apps

- Then run index and validate results
- Create dashboards for KPIs like errors, response times, traffic. Define alerts

- "To set up Splunk for an application, we first create an index in Splunk, then install a Universal Forwarder on the app host or configure HTTP Event Collector for containerized apps. We define the sourcetype, configure inputs to monitor log files or streams, and send them to Splunk indexers. Once data is ingested, we validate via Splunk search and build dashboards and alerts for monitoring."
