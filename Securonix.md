# ✅ Securonix L1 Technical (10 Questions) 30 Minutes

How to setup a terraform project for multi environment setup like de, staging, prod?
-
- We can use modules structure which is collection of .tf files in a folder. We can reuse building blocks
- We can call same module in different environments with different input variables
- It avoids code duplication, easier to maintain changes, we can have clean separation of env configs

- We can also use workspaces which is namespace for our terraform state file
- By default terraform has only default WS. But we can create new so each gets its own separate state file
- We can use same code but variables can change depending upon workspace

---------------------------------------------------

Can you exlain using terraform workspaces vs using separate state files?
-
- WS allows multiple statefiles inside same backend
- Here we can have one codebase resulting in less duplication, also easy to create new envs quickly. Statefile is handled auto by terraform

- Each env explicitly defines own backend config like S3 as remote backend
- Here we can have explicit separation. Its safer as less likely to make mistakes

<img width="761" height="548" alt="image" src="https://github.com/user-attachments/assets/d41ea01f-8eea-4bac-b82a-789ae3b1d012" />

---------------------------------------------------

As you have automated creating configurations using terraform, how to manage state locking and prevent conflict when multiple people are working on same terraform project?
-
- We can use combo of remote statefile and state locking for this
- Instead of keeping statefile locally, we can store it in shared remote backend like S3.
- Terraform holds lock for one person's apply when multiple people are trying to appky config. One's apply will be done and then lock will be released so other person can apply changes

- Here we can use S3 + DynamoDB

<img width="546" height="229" alt="image" src="https://github.com/user-attachments/assets/d8eae1ff-5e84-4f5a-98c0-c75ed1922feb" />

- Create S3 for backend and dynamoDB table for locks

- If someone tries to run terraform while lock is active, they'll see error :- error acquiring state lock

---------------------------------------------------

If I have created resources on AWS using terraform and someone modified them manually in UI. How to tackle this?
-
- First run terraform plan to compare state file vs real resources
- Reapply the tf file to remove manual changes and update to the file changes
- If resource exist in AWS but not in tf file, terraform ingores it. So we need to import that
  - Command :- **terraform import aws_instance.web i-1234567890abcdef0**

- To avoid this, prevent manual changes providing IAM permissions, using drift detection tools like terraform plan

- We can also use refresh apply here
  - Refresh will update terraform state file with real world values from provider AWS. If we manually change EC2 type, refresh updates state so terraform knows current value
  - Apply will apply config to reach desired state
  - If config differs from reality, terraform will create/update/destroy resources to match code

---------------------------------------------------

You have EC2 running in AWS and somehow root disk of that machine got corrupted. After reboot as well server is not coming up. How to restore that server?
-
- Go to EC2 and stop problematic instances (dont terminate)
- Detach root volume. Find root EBS volume
- Launch temporary healthy EC2 instance and attach the volume to it
  - Then ssh into instance and check logs and fix that volume
 
- Once fixed reattch that volume to our EC2 and start original instance

---------------------------------------------------

How to manage secrets in EKS clusters securely?
-
- We can store them in AWS secrets manager and mount them into pods dynamically. Pods can reference secrets from secrets manager. Secrets are mounted as volumes or env vars into pod at runtime
- Use hashicorp vault. Use vault agent injector to inject secrets into pods
- We can also use IAM roles for service accounts IRSA

---------------------------------------------------

Explain strategy to setup ingress controller in EKS and doing SSL termination
-
- K8S Ingress is just set of rules. To enforce them we need IC
- We can use AWS LB controller to provision ALB/NLB as inress points. Also we can use NGINX IC

- AWS LB Controller integrates with AWS. Each IR maps to AWS ALB
- It supports path/host/route based routing, SSL termination at ALB. Its also cost efficient

- NGINX IC gives flexiility. Need LB service. It Terminate SSL at NGINX or at AWS LB.

---------------------------------------------------

If pods are stuck in crashed loop backoff, how to troubleshoot?
-
- Identify pods and check for events :- kubectl get pods and kubectl describe pods. Check for events like OOMKilled, permission issues, probe failures
- Check container logs to find app level errors

- Causes
  - Application misconfigurations like wrong CMD/EP in dockerfile, missing env-vars, secrets
  - Probes crash fail - Adjust initialDelaySeconds and timeoutSeconds or probe path
  - Resource limits - Container exceeds CPU/Memory limits
  - Image/commands issues
  - Crash in init containers so app pod never start
 
- Debug with kubectl exec

---------------------------------------------------

Suppose our subnet is having IP exhaustion issue but new pods need to be launched, how to tackle this?
-
- Increase subnet size (CIDR)
  - In AWS we can add secondary CIDR block to our VPC. Add new subnets into that CIDR and update node groups to use those subnets
 
- Use CNI plugins like calico, AWS
- For prod, always allocate larger CIDRs (/16 or /18) to worker subnets if we expect many pods

---------------------------------------------------

Suppose one application is hosted in one AWS account and other application is in different account. User tells application is not accessible or they're unable to reach URL. But all configs are looking good. How to troubleshoot?
-
- Check how both apps are communicating using public internet, private or hybrid
- Verify if domain resolved to expected LB/IP
- Check if SG allows inbound traffic from other account CIDR, also check NACL

- Check if VPC peering allows DNS resolution + cross-VPC traffic. Check route tables, SGs
- Check IAM and resource policies

- First, I’d check DNS resolution, then network reachability (SG, NACL, routes, VPC peering/PrivateLink), then LB target health, and finally cross-account IAM policies. Many times the issue is private DNS or overly restrictive SGs.

---------------------------------------------------

