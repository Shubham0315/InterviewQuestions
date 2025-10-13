# ✅ L1 Helpshift - Keywords Studios Technical ( Questions) 59 Minutes

Can you tell about day to day activities you perform as a DevOps/SRE?
-
- Frequent question

---------------------------------------------------

Can you explain local blocks and data sources blocks in terraform?
-
- Locals are used to define local cars inside terraform config. They help us avoid repetition, simplify complex expressions and make code more readable
  - We define them once and can use many times.
  - Cannot be changes using variables or overridden at runtime
  - Best for static values used multiple times in config

<img width="757" height="518" alt="image" src="https://github.com/user-attachments/assets/9c7904bb-9c25-42e5-9ff0-5affd100ffe3" />

- Data sources let terraform fetch or read existing info from cloud provider or env without creating new resources
  - Its a way to query your infrastructure
  - Use caes are fetching AMI ID, reading details of existing VPCs, subnets and IAM roles
  - Data blocks dont create or modify resources, they read existing data from our environment
  - They're useful in hybrid and sared environments
 
<img width="777" height="415" alt="image" src="https://github.com/user-attachments/assets/a44d92ee-f6de-47c7-9f51-d9fb409d8777" />

---------------------------------------------------

Can you tell me basic difference between count and for_Each in terraform?
-
- Both control how many instances of resource are created

- Count is used when we want to create multiple instances of resource based on number or list
  - It works only with list or numbers and is index based means if order changes, terraform may destroy and recreate resources

<img width="771" height="328" alt="image" src="https://github.com/user-attachments/assets/ee74d80f-6688-4e78-ad0b-329c96dde79a" />

- For_each is used when we want to create multiple instances of resource from map or set, where each instance has unique key
  - Each resource has stable key so terraform wont recreate everything if the order changes

<img width="766" height="452" alt="image" src="https://github.com/user-attachments/assets/14b874be-20b3-4b25-a151-cf0aa56141f6" />

---------------------------------------------------

How do you structure terraform for larger environment? How does your directory structure look like in a complex environment wrt modules, workspaces, statefiles?
-
- We have modules in place for having reusable building blocks of code. Each module has its own main.tf, variables.tf, outputs.tf. Modules are called from env level configs which we define at root level by source
- For envoironments we use workspaces each having its own state file and backend config. So we have different variables for different environments
- We do maintain separate state file per environment to prevent collision between teams and it also allows rollbackc per environment

<img width="747" height="218" alt="image" src="https://github.com/user-attachments/assets/63da8682-cf9f-4e48-81d3-e5afc1aa557b" />

---------------------------------------------------

If we define modules at root level and reference them, what do you think will be the problem?
-
- Defining modules at root level can cause big issues in complex infra
- This can lead to no environment isolation. Statefile will include everything. We cannot separate dev, prod configs. Terraform apply could accidentally change prod resources
- This also leads to single state file. All modules share monolithic statefile. Any corruption, lock to it can affect entire infra
- Also they're hard to reuse across environments

---------------------------------------------------

How do you design a good state management using S3 and dynamoDB? 
-
- Terraform stores infra info in state file which is by default locally maintained. But its very risky in real world due to no locking, backup/versioning
- So we need to store it remotely using S3 and DynamoDB
- DynamoDB is for state locking to prevent concurrent updates

<img width="801" height="327" alt="image" src="https://github.com/user-attachments/assets/a3ea89b4-afc3-413a-ab8b-bbb20ead3f0b" />

- In S3 we can enable versioning so it keeps history of all state file changes. Rollback is possible
- Block public access to prevent public exposue and restrict policy access

- If someone tries to run same state, they'll get error if DynamoDB is in place for locking. Lock gets auto released once operation is completed


---------------------------------------------------

What are the steps to remove or disable lock? What set of commands does terraform have for the same?
-
- When we use remote backend like S3 + DynamoDB, terraform auto places lock in DynamoDB whenever we run commands
- When we do terraform apply, it contains lock ID section if apply is getting failed

- To remove or disable lock
  - Use terraform force-unlock. Use if no one is running terraform on that state file :- **terraform force-unlock $Lock_ID**
  - Disable locking temporarily :- **terraform plan -lock=false**
  - Manually delete lock from DynamoDB, if terraform force-unlock fails.
 
---------------------------------------------------

How do you handle secrets in terraform?
-
- Frequent questions
- Hashicorp vault and AWS secrets manager

---------------------------------------------------

Can you tell me a practical example of how to deploy a AWS VPC using terraform scripts?
-
- Frequent question about VPC architecture and modules
- Define VPC, subnets, SG, NACLs along with CIDR's for each and all resources should have vpc id attached inside specific resource block to identify resource belonging to which VPC
- Rest is below architecture

<img width="1848" height="621" alt="image" src="https://github.com/user-attachments/assets/035ee314-c282-427d-9d39-8dc0ec43ed96" />

<img width="1849" height="488" alt="image" src="https://github.com/user-attachments/assets/bb457e73-aa59-46ea-81c8-f60e3fcad539" />

---------------------------------------------------

Difference between CPU utilization and load average in Linux
-
- CPU utilization tells us how much time CPU spends working vs idle
  - If its 90% means CPU is busy 90% of the time, but not necessarily overloaded
  - Commands to check top and vmstat
 
- Load average tells us how many processes are waiting for CPU time or I/O at given moment
  - Commands to check is uptime
 
---------------------------------------------------

Can you tell me how to find load average of any system in linux? what are 3 main numbers in load average?
-
- Using uptime command
- It represents system load over last 1, 5, 15 mins
- load average: 0.80, 0.65, 0.50
  - This represent average load in last 1,5, 15 mins

---------------------------------------------------

How do you find out most I/O consuming processes in linux?
-
- Using iptop command which shows real time disk I/O per process
- Using top command it shows CPU and memory. So we can use htop
- Using lsof command to see which files a process is reading/writing

---------------------------------------------------

How to check connectivity between 2 instances?
-
- telnet command :- **telnet IP PORT**
- Ping command :- **ping IP**
- ssh test :- **ssh user@IP**

---------------------------------------------------

How do you identify if your system is capable of handling memory related workloads? How to check if its choking on memory?
-
- Check total memory and usage using free command
- Check memory usage per process using top command
- Check swap memory. High SWAP means system is running out of RAM
- Check for OOM events in logs

---------------------------------------------------

Can you explain some TCP connection states like what connection state you usually see when you see charts of TCP connections active ?
-
- ESTABLISHED :- Connection is open and data can flow. Active sessions between client and server
- LISTEN :- Server is waiting for incoming connections. Nginx listening on port 80
- CLOSED :- No connection exist
- TIME_WAIT :- Connection fully closed, short delay before freeing socket 

- We can check this using netstat command

---------------------------------------------------

Command to check what connections are active in your machine
-
- netstat command
- n to show numeric IP/ports, t for TCP connections
- lsof to list open network connections

---------------------------------------------------

How do you kill process and what exact flags we can use for it?
-
- kill PID :- sends SIGTERM signal 15 to gracefully terminate process
- kill -9 PID :- SIGKILL signal to force kill
- killall Process_name :- to kill all isntances of that process

---------------------------------------------------

