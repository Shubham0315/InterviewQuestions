# ✅ L1 T-Systems Technical ( Questions) 40 Minutes

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

