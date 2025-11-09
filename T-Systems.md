# ✅ L1 T-Systems Technical (30 Questions) 40 Minutes

One of the production Linux server doesnt boot properly and its stuck. What steps will you take to troubleshoot?
-
- I will first identify the failure stage
- Check the last boot logs from /var/log/messages or /var/log/syslog. Focus on disk errors, failed services, filesystem mount issues
- Verify if boot is stuck at mounting root or other partitions
- Check disk space and inodes. If /root is full, system might fail to start services :- **df -h** or** df -i**. If its full remove logs or temp data
- Reinstall or repair GRUB
- Disbale problematic services if it hangs at specific systemd service :- **systemctl disable $service**
- If system cannot be recovered quickly and its prod, restore te last known good snapshot or redeploy from IaC if applicable

------------------------------

There is a critical linux server and disak usage is 100% and application is stopped. How to troubleshoot?
-
- When disk usage reaches 100%, the application and system services can hang or crash because the OS can’t write to logs, temp, or cache directories.

- Check which mount point is full :- **df -h**
- Identify large directories :- **du -hs | sort -rh**
- Free space removing old logs or files :- rm -rf *.gz
- Verify after cleanuo :- **df -h**
- Bring application up and check logs  :- **systemctl start $service**

- Optionally set shell scripts or alerts when mount point is about to be full

------------------------------

Your system is not able to login from customer side and issue is showing as permission denied. How will you troubleshoot?
-
- This could be due to SSH issues, permissions or account problems
- Check SSH connection is proper
- Check network connectivity using telnet or ping
- Check ssh service status on server and start if required :- **systemctl status sshd**
- Verify if user exist :- **id username**
  - If doesnt exist create one :- **useradd $username**
- Ensure .ssh folder and files have correct permissions (700 or 600) :- ls -ld /home/username/.ssh
- Check /etc/passwd for username entry
- Check disk space, if full login fails

------------------------------

Suppose any service fails to start in Linux system, what will you check and troubleshoot?
-
- Check service status :- **systemctl status $service**
- View logs, check config files. If syntax error exist, correct it and restart service
- If logs mention address already in use, another process may be occupying the port :- **netstat -tulpn | grep <port>** or **lsof -i :<port>**
  - Kill or stop conflicting process :- **kill -9 $PID**
- Check files/folders permissions using ls command. Fix permissions if required
- Check disk space and memory :- df and free
- Restart the service

------------------------------

If any filesystem is corrupted, what things will you check?
-
- If a filesystem is corrupted in a Linux system, it can cause data loss or make the system unbootable.
- Identify the problem checking system logs and error messages :- **dmseg | grep -i error**
  - Check for I/O error, mount error
- Check which filesystems are mounted and teir status :- **mount | column -t and df -hT**
  - If any filesystem us missing, unmounted or showing read only, it might be corrupted
- Restore data from system snapshots, backup servers, DR site

------------------------------

Any cronjob didnt work properly, how will you troubleshoot? Log path for cronjob
-
- Verify cron service is running :- **systemctl status cron**
  - If inactive or failed :- **systemctl start cron**
 
- Check if cronjob exist :- **crontab -l**
- Validate cron syntax is proper
- Check script permission and paths
- Check cron logs :- **grep CRON /var/log/syslog**
- Manually run script, if fails fix the errors manually. There can be issues like missing PATH or variables

------------------------------

Suppose your ALB has issue as 504 gateway issue, how will you troubleshoot?
-
- A 504 Gateway Timeout from an AWS Application Load Balancer (ALB) means the ALB didn’t get a timely response from the target (EC2, ECS, etc.) before the idle timeout expired.

- Check target health if unhealthy targets. If all targets are healthy, issue is likely with application latency
- Verify health check config /health, /status
- Check app logs :- **tail -f /var/log/nginx/error.logs**
- Check SG and NACL if its blocking traffic
- Check direct access to target using curl :- **curl http://<target-private-ip>:<port>/health**

------------------------------

How will you secure S3 bucket?
-
- Block public access
- Use bucket policies carefully, fine grained access
- Enable SSE S3 or KMS encryption so data is encrypted before uploading
- Enable access logging and versioning

------------------------------

Is cross zone replication is possible in S3 bucket?
-
- No
- But Cross-Region Replication (CRR) and Same-Region Replication (SRR) are available.
- This is due to :-
  - Availability Zones (AZs) are within the same region.
  - S3 automatically stores data redundantly across multiple AZs by design.
 
- CRR Replicates objects to another region for disaster recovery or compliance.

------------------------------

How do you setup custom VPC in terms of security?
-
- Frequent VPC setup question
- Explain VPC, subents, IGW, SG/NACL, Route tables

------------------------------

ASG is not launching instances. How to fix this issue?
-
- If your Auto Scaling Group (ASG) is not launching instances, it usually points to a configuration or dependency issue
- Check ASG :- aws autoscaling describe-scaling-activities --auto-scaling-group-name MyASG
  - Look for failed to launch, access denied, no available subnets
 
- Verify launch template having correct AMI ID, instance type, key pair, SG and IAM role
- Check subnets and AZs. Ensure subnets belong to same VPC, not out of IPs
- Validate SG and IAM roles
- Check limits of EC2 per region or instance type. If exceeded, open service quota increase request

------------------------------

I have created 2 subnets in a VPC and choose /28 as my CIDR range. I have created 14 servers in it and want to add more servers. Is it possible to add CIDR range?
-
- Each /28 subnet provides 16 IP addresses total, but only 11 usable (5 are reserved by AWS).

- To add more servers we can add new subnet with different CIDR range. We cannot expand subnet's CIDR range once created but we can add new subnets inside same VPC so we get more IPs to use
- We can also add secondary CIDR block to VPC if our VPC's original CIDR is too small. Then create new subnets within this new range
- We can also create new subent with larger range and move EC2 into it deleting older subnet

- You can’t resize or expand an existing subnet’s CIDR — only add new ones.

------------------------------

You need a compliance retention of 7 years in my S3 storage. How will you design your S3?
-
- Here we need data immutability so no one can alter or delete data, auto retention policy for 7 years and cost optimization

- Create S3 bucket - Enable object locking and versioning
- We can apply retention policy globally or per object as well

<img width="784" height="467" alt="image" src="https://github.com/user-attachments/assets/9db980da-dd29-4e13-b4bd-29b5b6edc979" />

- Add lifecycle policies for cost optimization so once data is older we can move it to cheaper storage classes automatically. Deep archive after 1 year
- Then enable access logging - Block public access - Enable SSE S3 or KMS for data encryption

------------------------------

EC2 went down and I want alert triggered. How to configure in AWS?
-
- Use Cloudwatch to monitor metrics like StatusCheckFailed_System, StatusCheckFailed_Instance, StatusCheckFailed
- Create cloudwatch alarm - Alarms - Select metric (EC2 - Per-Instance metrics - StatusCheckFailed) - Set condition - Choose SNS to send notification
- Create SNS topic and subscribe on mail
- Then test alarm if SNS sends you an email or SMS alert.

------------------------------

I want to have certain entries without entering config file in Linux. How will I do that?
-
- Instead of opening a file in an editor (vi, nano), you can append text directly using shell commands.
  - echo shubham > /etc/hosts
  - sed or awk command to update parameter
  - sed command to replace text inside a file
 
------------------------------

Can you explain VPC peering?
-
- VPC Peering is a networking connection between two VPCs (Virtual Private Clouds) that allows them to communicate privately as if they were on the same network — using private IP addresses, without going over the public internet.
- Peering is required for VPCs to communicate, Apps in one VPC to access DBs or APIs in other, cross account or cross region private communication
- We can do peering between sam, different AWS accounts or VPCs in different regions

- Create VPC peering connection - Accept peering request - Manually add routes in both VPCs route tables - Update SGs

------------------------------

My server becomes unresponsive due to OOM exception. How will be your approach to troubleshoot?
-
- When your server becomes unresponsive due to an OOM (Out Of Memory) exception, it means the kernel ran out of memory and killed processes to recover.

- Check system logs to verify its OOM issue :- **grep -i "Out of memory" /var/log/messages**
- Identify which process was killed (name and PID of process) :- **ps aux --sort=-%mem | head**
- Analyse memory usage :- **free -h**
- If swap is disabled or exhausted, OOM triggers faster
- Collect metrics to detect memory growth over time. Set alerts before memory is fully consumed.

------------------------------

I accidentally deleted /etc/passwd file. I want to recover the same. How will you do it?
-
- Without /etc/passwd file we cant login, commands may fail and SSH login may also break

- We can recover directly using root user rebooting the system
- Linux systems always have backup of tis file :- **ls -l /etc/passwd-**
- Restore from backup :- **cp /etc/passwd- /etc/passwd**
- If it doesnt exist, recreate with minimal entries

<img width="699" height="190" alt="image" src="https://github.com/user-attachments/assets/282bf83d-e581-4946-b0f5-7cc91ed79c3f" />

------------------------------

My system suddenly gets rebooted, how to check what happened?
-
- Check system uptime. If its small it confirms recent reboot :- **uptime**
- Check last reboot history :- **last -x | grep reboot**
- Check kernel logs :- **cat /var/log/syslog | grep -iE '(panic|error)'**
- Check for OOM :- **grep -i "Out of memory" /var/log/syslog**
- Check cloud provider logs if VM

------------------------------

I updated my kernel and rebooted. Now system drops to kernel panic like unable to mount rootfs throwing unexpected error. In this case what can be done?
-
- This error generally means
  - new kernel cannot find drivers or modules to mount / filesystem
  - kernel boot parameters point to wrong root device
 
- Check missing initramfs :- **ls /boot**
- Check if corrupt filesystem :- **fsdk -f /dev/sdaX**
- If grub is misconfigured :- **update-grub**

------------------------------

What do you understand by mounting a disk persistently?
-
- When you mount a disk (or partition), you are making its filesystem accessible in your Linux directory tree — i.e., attaching it to a specific directory (called a mount point).
- Command :- **mount /dev/xvdf1 /data**

- If we mount disk manually with mount command it exists only until next reboot, after reboot we need to mount it again
- Persistent mounting means the disk will automatically mount every time the system boots, without needing manual intervention.

- Create mount point :- mkdir /data
- Update /etc/fstab with UUID which ensures mount stays valid even if device name changes after reboot

------------------------------

I want to resize partition in Linux machine. What is the command used?
-
- fdisk or parted

<img width="730" height="217" alt="image" src="https://github.com/user-attachments/assets/2a4fbf99-d051-49db-903b-5f65bec2b819" />

------------------------------

Can you explain different kind of EBS volumes in AWS?
-
- EBS (Elastic Block Store) provides persistent block storage for EC2 instances.
- Its a virtual hard drive which persists even after instance start/stop, can be attached.detached to EC2

- gp2 :- general apps, 3 IOPS per GB max
- gp3 :- modern gp2, upto 16000 IOPS configurable, 20% cheaper than gp2

------------------------------

Can I attach same disk on different servers when I want to mount share filesystem with AWS?
-
- You cannot attach a single EBS volume to multiple EC2 instances at the same time
- An EBS volume can be attached to only one EC2 instance in the same Availability Zone (AZ) at a time. It provides block-level storage — not a shared filesystem.

- AWS allows EBS Multi-Attach for io1 and io2 volume types only.You can attach a single EBS volume to up to 16 EC2 instances (within the same AZ).
- To share files between instances use EFS, Can be mounted by multiple EC2 instances across multiple AZs.

------------------------------

What is the difference between snapshot and AMI?
-
- A snapshot is a point-in-time backup of an EBS volume (block-level copy). Stores data of EBS volume at specific moment
- An AMI is a pre-configured image used to launch new EC2 instances (like a template for servers). Contains root volume snaoshot of OS config. Used for creating identical EC2 instances quickly.

------------------------------

From snapshot or AMI can I recover whole VM?
-
- No, you cannot directly recover a whole VM (EC2 instance) from a snapshot alone. But you can recreate the instance manually from that snapshot — because a snapshot only contains the EBS volume data, not the full EC2 configuration.
- Yes — from an AMI, you can recover the entire VM (EC2 instance) easily. If your original EC2 instance crashes or is deleted, you can recreate it 100% using the AMI.
- EC2 - AMI - Select AMI - Launch instance - Choose instance type - Launch

------------------------------

If I want to deploy application on EC2 which required HA, what will be your approach?
-
- High Availability (HA) means your application continues to run even if one or more components fail — no single point of failure
- Launch your EC2 instances in at least two AZs within the same AWS Region. Each AZ has independent power, cooling, and networking.
  - This ensures if one AZ fails → traffic automatically routes to the other.
 
- Use ALB for EC2 so it Distributes incoming traffic across instances in multiple AZs and auto removes unhealthy instances from rotation
- Use ASG with min 2 instances and max 4-6 based on load. Configure ASG to lanch EC2 in multiple AZs, replace failed instances and scale them based on metrics

------------------------------

Have you ever implemented SSL and TLS certificates?
-
- No idea

------------------------------

What kind of security did you implement while configuring load balancers?
-
- Use HTTP/S encryption of requests for ALB/NLB
- Restrict access using SG
- Enable Web application firewall (WAF)
- Enable access logs for LB which help in auditing, security investigations
- Apply IAM and least privilege
- Enable cloudwatch alarms

------------------------------

Difference between AWS managed and custom managed policies?
-
- AWS managed polices are pre-defined, maintained by AWS while custom managed are created by us
- AWS managed are easy to use as no need to write JSON but we cant edit them
- Custom managed are written bt us in JSON defining specific actions, resources and conditions. They've least privilege and we decide when to update. It Requires more IAM knowledge.

------------------------------


# ✅ L2 T-Systems Managerial ( Questions) 19 Minutes
