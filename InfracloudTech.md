# Infracloud Technologies Technical

What branching strategies do you use in your current project
-
- Git flow branching strategy :- release, prod, hotfix, dev

--------------------------------------------------------------------

What is the difference between rebase and merge in GIT?
-
- In GIT rebase and merge are 2 ways to integrate changes from one branch to another.

- **Merge**
  - Combines 2 branches by creating new merge commit that has 2 parents keeping history of both the branches intact
  - It is non linear but shows breanch structure with all commit history
  - We can merge feature branch to master
 
- **Rebase**
  - 2 step process :- add feature branch on top of master and merge feature to master
  - When we execute "git rebase master", git internally creates 2 new commit objects for 2 commits in feature discarding commits in feature. New commit objects will be created on top oif master
  - Then we need to merge them to master :- git merge feature
 
- When rebase is used, no chances of getting conflicts
- Everytime we can go for merge whether there are conflicts or not. After merging and resolving we can push to remote. In normal merge, history is also preserved
- Rebase can be only used within local. Only you are responsible for your changes and can create multiple branches and rebase them as needed. So advisable not to use rebase for remote repository as multiple developers can be using the master branch. In rebase history is not maintained as feature gets vanished

--------------------------------------------------------------------

What are the different deployment strategies?
-
-** Recreate (Basic) Deployment**
  - Stops old version completely and starts newer. Simple and straight
  - Downtime can occur, not suitable for prod. Used in small apps or non critical envs
 
- **Rolling Deployment**
  - Gradually replaces old instances with new ones, one instance at a time
  - No downtime, users can access app during deployment as well
  - Old and new versions run together, there can be compatibility issues
  - Used for apps where gradual rollout is applicable like K8S
 
- **Blue-Green Deployment**
  - Maintains 2 identical envs. One live and other for new version development
  - We can switch traffic from blue to green once ready
  - Minimal downtime and easy rollback as older version is there due to identical envs
  - Needs double resources, so costly
  - Used in HA prod systems
 
- **Canary Deployment**
  - Releases new version to small subset of users first, monitors and then gradually increases traffic
  - Reduces risk as can detect issues before full rollout
  - More complex setup, needs monitoring
 
--------------------------------------------------------------------

What is the jenkins architecture?
-
- Jenkins is CICD automation server that helps automate building, testing and deploying apps.
- Jenkins follow distributed, master-slave architecture

- **Master**
  - Central control unit
  - Used to schedule jobs on worker nodes, monitoring slaves, storing build results, providing web interface for managing jenkins and configuring jobs
 
- **Slaves**
  - Worker nodes that perform the actual build/deployment which master schedules on them
  - Executes build scripts, deployment commands, reports result back to master so master stores it
  - Agents are connected to master via SSH
 
- When multiple jobs are triggered simultaneously, they enter in queue in master and are executed on available agents based on priority and availability
- Each agent have executors which are slots to run parallel jos

--------------------------------------------------------------------

If you have hundreds of worker nodes and want to run hundreds of jobs concurrently with almost no queuing, how to do that in jenkins?
-
- Add more agents - Horizontal scaling
  - Each jenkins agent has limited no of executors to run jobs. If jobs exceed available executors they go in queue
  - So we can add more agents or increase executors per agent
 
- Use EC2/Cloud plugins (Auto scaling)
  - Jenkins master spins up new EC2 instances when queue builds up and shuts them down when idle
 
--------------------------------------------------------------------

What are different types of jenkins jobs?
-
- Jenkins job is a unit of tasks jenkins executes like building code, testing, deploying app.

- Freestyle :- Basic. We can configure job as needed. Pull code - Run commands - Archive results
- Pipeline :- Job defined as code in jenkinsfile used for CICD. Multiple stages
- Multi branch pipeline :- Auto discovers branches in Git repo and creates pipeline job for each branch with jenkinsfile
- Maven project :- for java projects, jenkins understand maven lifecycle.

--------------------------------------------------------------------

What is a multi-branch pipeline?
-
- Its a special type of pipeline job that auto discovers, manages and executes jenkins pipelines for multiple branches of repo.

- When we provide repo, jenkins auto scans it and for jenkinsfile in each branch it creates separate pipeline job
- When new branch is created or deleted in repo, jenkins updates auto via webhooks and periodic scans

- Imagine having main, dev, feature branches. Jenkins auto creates jobs for all

--------------------------------------------------------------------

What are different types of networks in docker?
-
- **Bridge**
  - Default networking in docker. Containers on same host communicate with each other in isolated network
  - If we've container mounted on host OS and we have to ping it from host, we get error as both container and host have different IP ranges due to different subnet
  - So docker creates virtual ethernet so container can talk to host. This is Bridge networking where veth acts as bridge
  - Containers on same bridge network can communicate with each other using container names where as containers outside network cannot access them directly without port mapping
 
- **Host**
  - Removes network isolation and binds container directly with host
  - When we create container, container shares host network NS
  - Here both host and container are in same subnet and have same IP ranges so we can ping as well
  - If user has access to host, he can directly access app inside container - Insecure
 
- **Overlay**
  - Enables multi host networking for docker swarm services. Useful in docker swarm and K8S when we've multiple hosts
 
- Commands :- **docker network create --driver=overlay shubham** (--driver not needed in bridge as default)

--------------------------------------------------------------------

Assume that you have a dockerfile in which you pass username and password. When doing docker inspect, we can see credentials. So how we can avoid this?
-
- If we put our creds in dockerfile, they get backed into image layers. So anyone with access to image can see them via inspect command. Even if we remove them, they remain in history layers

- Use docker secrets
  - Store secrets in docker secrets or K8S secrets
  - Secrets are mounted into container at runtime. So they're not visible in inspect
  - In ECR, we can use AWS system and secrets manager
 
- Use env vars at runtime
  - Dont hardcode dockerfile, pass at runtime
  - Or store in .env file
 
- Use external secret managers
  - Vault, AWS secret manager
 
- Multi stage builds
  - If creds are needed only for build time, we can use ARG in multi-stage builds and dont copy them to final stage
 
--------------------------------------------------------------------

What is difference between ADD and COPY in dockerfile?
-
- Both are dockerfile instructions that put files into image, but there are some differences

- COPY
  - Copy files/folders from local build context into image
 
- ADD
  - More powerful
  - We can extract tar archives automatically :- ADD app.tar.gz /usr/src/app
  - Can fetch files from remote URLs, downloads file from internet
 
--------------------------------------------------------------------

Can the ENTRYPOINT in dockerfile be overridden?
- 
- EP defines main executables of container which cannot be overridden easily using docker run unless we pass --entrypoint while running commands
- Current EP :- ENTRYPOINT ["echo", "hello"]
- This can be overridden :- docker run --entrypoint ls ubuntu

--------------------------------------------------------------------

What is multi stage docker file?
-
- Allows to use multiple FROM statements in single dockerfile
- Early stages are used for building, compiling, testing which include heavier images. While final stage consist of minimal runtime files leading to smaller and more secure image

<img width="743" height="295" alt="image" src="https://github.com/user-attachments/assets/5146ba76-8f43-46a7-ac89-e3d52986ad26" />

- We use AS buildder in first stage and only necessary artifacts are copied to final stage
- In 2nd stage we use --from=builder to copy artifacts

- Here compiler and build dependencies are not shipped, only binary is copied to final stage

- Multi stage provider clean separation between build and runtime env
- Saves space and increase security

--------------------------------------------------------------------

How many stages we can pass in multi stage docker file at max?
-
- No hard limit
- We can have as many FROM statements
- If more stages - bigger dockerfile - slow builds due to caching
- Each stage increase complexity

--------------------------------------------------------------------

While installing docker on local there are predefined storage area. If you want to alter it, can we alter?
-
- On linux docker stores images, volumes :- /var/lib/docker

- If /var run out of space, we need to change it

- Stop docker, move existing data, configure new storage path, restart docker and verify

--------------------------------------------------------------------

