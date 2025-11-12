# ✅ L1 Persistent Technical (5 Questions) 15 Minutes

Can you tell me any interesting work you did lately? What was the solution and your role involvement?
-
- Frequent question depending on work experience
- Can explain 1-2 projects where automation was in place
- Setting up CICD pipeline instead of manual deployment on servers or Linux VMs
- Terraform infrastructure automation of CLOUD

--------------------------------------

Suppose your disk usage is very high and you want to see which directories or files are consuming more space. Which command will you use?
-
- du command
- To check disk usage in current directory :- **du -sh ***
- To find top space consuming directories :- **du -ah /path | sort -rh | head -n 10**
- To get disk usage of directory :- **du -sh /var/log**

- To check for entire filesystem and all mount points :- **df -h**

--------------------------------------

If you have to write shell script to monitor CPU usage which gives you alert when 80% is breached, how will you write that?
-
- Frequent shell scripting question
- Use threshold condition and mailing list where to send alerts
- DU=$(df -h | awk '{print $5} | tr -d %')
- mail -s "Disk space alert" $TO  

<img width="851" height="286" alt="image" src="https://github.com/user-attachments/assets/d3db82e0-6891-43bc-9b3c-58a453684368" />

--------------------------------------

You need to automate user creation in active directory in shell. How would you create it?
-
- On linux we need ldapadd / ldappasswd commands
- First install LDAP utilities :- **sudo apt install ldap-utils**
- Write below shell script having LDAP server details, input file and logic

<img width="760" height="731" alt="image" src="https://github.com/user-attachments/assets/35c183ac-b0e1-4a5d-b2c3-9f2d0e355a5a" />

- Run script :- **chmod +x users.sh** and then **./users.sh**

--------------------------------------

While using docker, container gets restarted and we need to have data within containers. How can we do that?
-
- Containers are ephemeral — if you remove or restart a container, data in /var/lib/docker/containers/... is lost.
- Use docker volumes. When we create volume and bind container to it, the data of container gets bound to host directory as well so even if container restarts we can push local data to new container
- Use bind mounts to host directory directly into container. We dont directly bind host folder to container folder but we create logical partition on container filesystem and data persist within it
- Volumes are for persistent volumes and prod env
- Bind mounts for Direct link to host files/folders for dev env

--------------------------------------


# ✅ L2 Persistent Technical (7 Questions) 27 Minutes

How is the monitoring done in your project using DataDog and Splunk? Is it using metrics or any other means?
-
- Frequent question. Explain about project and how logs are captured
- Give overview how you have set SLI, SLA and SLO for different metric like CPU, Memory, response time, error rates, latency
- Error tracking page for different microservices
- Pod and node health check, HTTP dashboard check for P90, P99 request

--------------------------------------

Explain the challenges you faced when you had to recover situation in production systems
-
- Frequent scenario question based on experience
- Manual to automated deployments using CICD - one example
- AWS resource creation using terraform scripts

--------------------------------------

Have you created infrastructure using terraform? How's your experience on creating backups as well using terraform scripts or modules?
-
- I’ve used Terraform to provision and manage various AWS resources like EC2 instances, VPCs, subnets, security groups, S3 buckets, IAM roles, and RDS databases.
- For backups we've RDS in our project using which we automate backup retention policies and snapshot creation through Terraform resources. Primary and secondary region are in place
- I’ve also written custom Terraform modules to standardize backup configurations across environments — ensuring consistency, easy reuse, and compliance with organizational policies.
- You can also explain how you create infrastructure in blue green setup if exist
- First we create infra using terraform and then for DR purpose we have identical environments and replicas deployed in multiple zones. So whenever any issue happens in any env, we switch the traffic to other env or zone making secondary region as primary for user traffic
- We also have modules written for different resources

--------------------------------------

You have mentioned you reduced deployment errors. How did you do that? 
-
- This was done by setting up CICD pipeline for applications which we were deploying manually
- We set up multiple stages in pipelines and deployed on on-prem as well as cloud servers

--------------------------------------

What is your core skillset ?
-
- General question
- Jenkins CICD, Kubernetes, Terraform, DataDog/Cloudwatch monitoring

--------------------------------------

Explain one best practice for SRE in your project
-
- General question reagrding application traffic and monitoring tools
- Error tracking, traffic monitoring, SLA/SLI, node/pod health checks, HTTP dahsboards for 400/500 errors

--------------------------------------

How is your experience on Linux administration and shell scripting
-
- In one of my roles I had to depliy apps on Linux machines. We had to check logs, application requests on different servers. There I worked as Linux admin as well
- We had implemented shell scripts for logs archiving, log rotation, CPU/memory usage, etc
- You can explain more points according to experience

--------------------------------------



