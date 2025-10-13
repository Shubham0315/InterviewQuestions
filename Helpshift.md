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



<img width="766" height="452" alt="image" src="https://github.com/user-attachments/assets/14b874be-20b3-4b25-a151-cf0aa56141f6" />
