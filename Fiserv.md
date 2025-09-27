# ✅ L1 Fiserv Technical (14 Questions) 32 Minutes

Can you give me command to find modified files in last 24 hrs and compress them into a single file? Give single command 
-
- To find files modified in last 24 hrs :- **find . -type f -mtime 1**
- To compress the output into single file :- **find . -type f -mtime 1 print0 | tar -czvf files.gz**


------------------------------------

If I want to replace a string in file with another string, which command to use?
-
- sed command - stream editor
- Command :- **sed -i 's/old/new/g' file.txt**
- It will substitute all occurences of old string with new string

------------------------------------

Using above command if I want alternate occurences of string to be replaced in a file, how to do that?
-
- Use sed with counter which replaces every 2nd occurence of old string with new
- Command :- **sed 's/old/new/2g' file.txt**

------------------------------------

What is the difference between $* and $@ in shell?
-
- Both represent positional parameters passed to script or function
- $* treats all arguments as a single string like "$1 $1 $3". Used when we want to preserve each argument exactly
- $@ treats each argument as a separate string like "$1" "$2" "$3". Used when we want to treat all arguments as single string

------------------------------------

How are you doing K8S auto scaling ? Describe HPA, VPA, CA
-
- We've EKS cluster using which we're deploying apps in EC2 instances.
- To scale pods we're using HPA, VPA and CA

- Horizontal Pod Autoscalar
  - Scales no of pods in deployment based on metrics like CPU, memory, request counts.
  - We set threshold in HPA files with setting min and max replicas based on which autoscaling is being done
 
- Vertical Pod Autoscalar
  - Adjusts CPU/memory limits of pods automatically
  - Good for workloads with unpredictable resource needs
 
- Cluster Autoscalar (Nodes)
  - To scale worker nodes in cluster. Works with cloud provider APIs
  - Used when pods cannot be scheduled on nodes due to lack of resources. CA requests cloud provider to add nodes automatically and move pods there to have zero downtime and HA
 
------------------------------------

How do you debug K8S pods with different commands?
-
- use exec command to go inside the pod and check for logs :- **kubectl exec -it deployment/nginx -- bash**
- Check pod status :- **kubectl get pods**
- Check pod issue/events with runtime logs :- **kubectl describe deployment/nginx**

------------------------------------

What are the different states of the VMs in AWS?
-
- Pending : Instance is being launched
- Running :- Instance is up and running, we can check workloads
- Stopping :- Instance is shutting down. EBS volume is preserved
- Stopped :- Instance is fully shut down. 
- Terminated :- Instance permanently deleted. EBS volume also deleted

------------------------------------

Suppose my VMs are standalone and I have trouble with VM start. What steps I will use to troubleshoot? Machine is in failed state
-
- Check instance state. See if in stopping or pending.
- Look at logs or console output and check for kernel issue, filesystem corruption, missing critical files
- Check for network and SG issues. Check if NACL stateless rules are blocking traffic
- Check storage and volumes if its corrupted. Then stop instance  - detach volume - attach to healthy instance - mount volume and check logs - fix issues in corrupted instance - Reattac to original instance and start again

------------------------------------

How are you managing state files in terraform?
-
- Using S3 and dynamo DB for remote state file and locking mechanism
- Frequent question

------------------------------------

How are you handling sensitive data in terraform or AWS?
-
- Frequent question
- Using TF vault and AWS secrets manager' parameter store
- Using vault create KV pair and mount in on filesystem on our EC2

------------------------------------

How you define host in Ansible inventory file ?
-
- In inv file /etc/ansible/hosts we define hosts by IP, hostname or alias
- Suppose we have web and db server and under which 2-2 hosts are there. Define groups for servers and provide hostnames under them

<img width="655" height="230" alt="image" src="https://github.com/user-attachments/assets/62014ba2-0b57-4389-a3b1-721d94ef3b54" />

- We can also use variables to define hosts. Define alias for IP and use in inv file

<img width="760" height="123" alt="image" src="https://github.com/user-attachments/assets/53f2b237-75a3-48e2-9445-c13ac3d2fe90" />

- We can also define servers in a parent-child relationship. If web and db need to be under all servers list, use below syntax

<img width="649" height="324" alt="image" src="https://github.com/user-attachments/assets/8eb692e9-21b3-4eeb-94d3-296cb9e9c80f" />

------------------------------------

Can we store variables in inv file?
-
- Ansible allows you to store variables in the inventory file, both per-host and per-group
- We can assign vars directly to host in inventory

<img width="633" height="120" alt="image" src="https://github.com/user-attachments/assets/7a35abdc-8da2-42e6-aab9-24d049425d32" />

- We can also define variables for group of hosts

<img width="640" height="228" alt="image" src="https://github.com/user-attachments/assets/23735e96-ed15-4fa1-afa4-9c28d4dc6638" />

------------------------------------

In jenkins, what are different stages are you using inside pipeline?
-
- Frequent question
- Code checkout - Build JAR / Build image - Push image to dockerhub - Scan image using sonarqube scanner - Deploy on EKS cluster

------------------------------------

Any idea about harness deployments?
-
- No work experience

------------------------------------

