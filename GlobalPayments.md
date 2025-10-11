# ✅ L1 Global Payments - TSYS Technical (16 Questions) 23 Minutes

What kind of linux administration experience do you have?
-
- Management of users, groups and permissions. Monitoring system performances and resources using top.
- Process monitoring using ps, kill. Automating tasks using cron jobs
- Disk usage monitoring using df, du and cleanup
- Network troubleshooting using ping, traceroute, netstatConfigure SSH access, key based authentications.
- Monitoring logs in /var/log/auth.log or /var/log/secure
- Writing bash scripts like backup, log rotation, cleanup for automation.

----------------------------------------------

Can you explain Linux file stucture or directory structure?
-
- Linux uses single rooted filesystem starting at root /. Every file and directory exists under it

- Directories
  - root
  - /bin :- contains essential command binaries used by all users like ls, cat, cp, mv
  - /dev :- contains device files that represent hardware devices
  - /etc :- for config files. /passwd for user accounts, /ssh for ssh server config
  - /home :- contains home directories for non root users
  - /lib :- contains shared libs needed by binaries
  - /opt :- third party or optional software lcoatoon
  - /sbin :- system binaries
  - /tmp :- temporary files created by users of processes
  - /usr :- user programs
  - /var :- contains files expected to grow/change over time like log files, app state
 
----------------------------------------------

In order to troubleshoot any issue, what types of var files do we monitor? If my server has booted, where the logs get recorded?
-
- /var/log :- logs for system, kernel and apps. Check service errors, auth issues, network problems in syslog
- /var/lib :- logs for data for services. Check DB files, package manager, K8S data
- /var/cache :- logs for cached app data. Check for caching issues or clean if disk full
- /var/tmp :- logs for temporary files preserved across reboots. Check for leftover temporary files causing disk space issue

- /var/log/messages :- general system messages, system events
- /var/log/syslog :- system manages and boot logs

----------------------------------------------

If user is unable to login to server, which log file should we check?
-
- /var.log/auth.log :- stores authentication and authorization messages, SSH attempts, sudo attempts
- /var/log/faillog :- tracks failed login attempts per user
- /var/log/messages or /var/log/syslog

----------------------------------------------

How to monitor linux server performance?
-
- CPU monitoring :- top, htop.
- Memory monitoring :- free
- Disk usage :- df and du
- Network monitoring :- ifconfig, netstat
- Process monitoring :- ps, uptime
- Log monitoring :- tail -f $logfile

----------------------------------------------

What is swap memory?
-
- SWAP is a disk space used as virtual memory when system runs out of physical RAM
- It prevents OOM crashes when RAM is full. Allows more processes to run simultaneously tahn RAM can handle. helps systems with low RAM run heavy apps

----------------------------------------------

What is difference between du and df?
-
- df :- disk free shows disk usage of entire filesystem
- du :- disk usage shows space used by directories or files

----------------------------------------------

Difference between IAM role and IAM user. Can we attach IAM role to IAM user?
- 
- Frequent question
- No we cant attach role to user as users are permanant and roles are temporary

----------------------------------------------

What is instance profile in AWS?
-
- Its a container that allows EC2 to assume IAM role. EC2 cannot directly assume IAM roles, they need instance profile as bridge. It is auto created when we attach role to EC2

----------------------------------------------

How many ways we can access EC2 instance in AWS?
-
- SSH using key pair
- Using EC2 instance connect (ssh -i .pem user@IP)
- Using RDP
- Via AWS console
- AWS System manager. Access without public IP or SSH keys

----------------------------------------------

To host static website using S3 bucket, what is the essential policy to attch for it?
-
- Key requirements are objects in bucket must be publicly readable so anyone can access them via website endpoint. We can attach bucket policy that allows public read access

<img width="771" height="633" alt="image" src="https://github.com/user-attachments/assets/2b8b5705-e5bf-467b-a91c-2b874711aa28" />

- For bucket - properties - static website hosting- Index and error pages
- Block public access
- After applying policy and enabling website hosting, access the site

----------------------------------------------

What is autoscaling?
-
- Frequent question
- Pod and node autoscaler using HPA, VPA, ASG

----------------------------------------------

What is use of health check in autoscaling?
-
- Health checks are critical to ensure EC2 instances in ASG are healthy and serving traffic properly. If EC2 fails health check, auto scaling can autp terminate it and launch new healthy instance to maintain desired no of pods. It ensure HA and resiliency of our app
- ELB health check integrates with ALB or NLB. Checks if instance is responding to traffic. Useful to detect app level failures
- Its used for HA and minimizing downtime, detect app level failures via ELB health checks

----------------------------------------------

Difference between VPC peering and transit gateway
-
- Peering is direct connection between 2 VPCs using private IP address. Traffic flows privately and directly without going over internet or VPC
- Transit GW is centralized hub that connects multiple VPCs and on-prem networks. Acts as router for VPCs enabling transitive routing
- Peering is one to one connection while transit GW is one to many connections

- Transit GW is used in large scale multi VPC architecture while peering used for small no of VPCs needing private connectivity

----------------------------------------------

What kind of LBs do you work on?
-
- Frequent question
- ALB, NLB, Gateway LB

----------------------------------------------

What is the difference between SG and NACL? Which is stateful and which is stateless?
-
- Frequent question



