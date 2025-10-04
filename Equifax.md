# ✅ L1 Equifax Technical SRE (19 Questions) 38 Minutes

What git commands do you use on daily basis as a DevOps Engineer?
-
- git clone $repo :- to clone remote repo to local
- git status :- to check current branch, staged files and untracked changes. To verify every change before commit
- git add $file :- stage modified files or new files to commit
- git commit :- git commit -m "message" :- to commit staged changes with message
- git push origin main :- to push local commits into remote branch
- git pull origin main :- fetch and merge changes from remote to local
- git fetch origin :- to fetch updates from remote wuthout merging automatically
- git branch :- to list, create, delete branches
- git checkout $branch :- to switch between branches or restore files
- git merge $feature :- to merge changes from one branch to other
- git log :- to view commit history and debug
- git diff :- to check differences between any 2 stages of git workflow
- git reset $file :- to undo changes or unstage files
- git rebase :- reapply commits from one branch to other base branch
- git tags :- to create version tags

--------------------------------------------------

How to rollback changes made in any branch?
-
- Undo local unstaged changes :- If we've modified file but havent staged or committed we can use git restore  or git checkout. It discards local modifications
- To unstage file :- If you add to staging but want to remove, use git reset
- Undo last commit keeping changes in work directory :- use **git reset --soft HEAD-1** . This moves HEAD to one commit back
- Completely discard last commit :- use **git reset --hard HEAD-1** .
- Rollback commit thats already been pushed to remote :- use **git revert $commit_id**. This creates new commit which undoes changes from old one. Safe as it preserve history and works well with shared branches
- Restore files from specific commit :- use **git checkout $commit_id**

--------------------------------------------------

If you have reset your main branch and lost all the commits, how will you recover that?
-
- If we've ran something like "git reset --hard HEAD-5", all recent commits in main branch are gone
- Git never permanently deletes commits immediately, they exist in reflog from where we can recover them

- Check reflog :- Reflog records every movement of HEAD
- Identify commits you've to restore. Note commit id from reflog
- Reset branch back to that commit id :- **git reset --hard $xsgdh**

- Avoid hard reset on shared branches. Before reset, take backup of branch :- **git branch backup-main-before-reset**

--------------------------------------------------

How to setup CICD pipeline to deploy artifacts to EKS cluster?
-
- Frequent question

--------------------------------------------------

In your CICD pipeline, how will you ensure deployment file has the latest image we pushed to ECR?
-
- Our pipeline builds docker image, pushed it to ECR and deploys on K8S using manifest files

- When we build image store image tag as variable inside pipeline. Then build and push. Also have image repo in place to store into ECR

<img width="614" height="237" alt="image" src="https://github.com/user-attachments/assets/18a55d98-800b-4088-bb53-e71bc3736b37" />

- Use kubectl set image inside deploy stage :-  kubectl set image deployment/nginx-app nginx-app=$IMAGEREPO:$IMAGE_TAG
  - This will ensure images inside deployment will be latest one
 
<img width="785" height="506" alt="image" src="https://github.com/user-attachments/assets/606ccd29-30a6-47fc-8700-320e00c82603" />

- Using jenkins we can also set the same
  - Setup region, ECR_REPO, image tag, deployment name, container name, kube config
  - Setup stages like below and in deploy stage export config, set image for deployments and then use rollout latest image to all replicas of deployment
  - Using this we dont need to edit YML files manually. It always deploys latest image built in same pipeline :- **kubectl rollout undo deployment/myapp**
 
<img width="773" height="387" alt="image" src="https://github.com/user-attachments/assets/5d49edaf-45fc-4b36-a8f2-f2a61a4dd0b3" />

<img width="719" height="437" alt="image" src="https://github.com/user-attachments/assets/35c7a712-4462-4d71-823f-4e143e648424" />

** Pro tip :- Use IMAGE_TAG = ${BUILD_NUMBER}

--------------------------------------------------

Please walk through the Disaster Recovery plans used in your application which circles around database
-
- In our application, DR primarily focuses on ensuring our DB remains available, consistent and recoverable even during regional outages, accidental deletions or corruption
- For DB instance failures we use automated failover.
- For region outage we're having cross region replicatiion and backup snapshots
- For accidental deletion we have point in time recovery from backups

- We've multi AZ replication in place for HA within region. Primary DB runs in one AZ and a standby replica is auto maintained in other AZ
- AWS RDS handles automatic failover to standby if primary goes down. Here no manual intervention is needed with minimal downtime

- Also we have cross region replicas. One replica as primary and other as DR. If regional outage is there, we promote DR replicato primary and point our app to it

- We've also leverages automated backups and snapshots in place. Daily snapshots are taken and retention policy is usually 15-30 days depending on environment
- Also we take manual snapshots before major release or upgrade

- Using backup storage replication, snapshots are auto copied to another region (DR) using lifecycle policies
- Ensures backups are available even if entire AWS region fails

--------------------------------------------------

Suppose one of the region fails which is used to store data in particualr DB. Now DB was in same region and its also not accessible. What is the fallback plan here?
-
- Our primary AWS region goes down including DB. App and users are now unable to read/write data as primary DB is unavailable
- AWS region failure means even multi-AZ setups wont help.
- We need cross region DR or failover plan

- Using cross region read repicas primary DB in 1st region replicates to read replica in 2nd region
- If region A fails, promote replica in region B to become new primary and update app endpoint to point to new DB endpoint. Using route53 failover routing policy so app reconnects to promoted DB and resumes operation

- If replicas not present use cross region automated backups
- Restore latest snapshot in secondary region

- If entire region is lost, use terraform to redeploy resources like EC2, RDS

- Use route53 failover routing policy with primary endpoint as region A DB and secondary endpoint as region B DB
- App auto connects to new region's DB and minimizes downtime and manual intervention

--------------------------------------------------

What is readiness and liveness probes in K8S?
-
- K8S uses probes to check health of containers running inside pods
- Both are defines using httpGet method


- Liveness probe checks if app is running properly. It detects app inside container has crashed or become unresponsive. If this fails, K8S kills container and restarts it automatically
  - K8S starts checking /health endpoint 10 seconds after container starts. If app doesnt respond with 200, probe fails and K8S restarts container

<img width="588" height="145" alt="image" src="https://github.com/user-attachments/assets/2a4be7d6-6b42-40d0-9ce8-25850c421e8a" />

- Readiness probe checks if app is ready to serve traffic. If probe fails, K8S removes pod from service's endpoint list so no traffic is routed to that pod
  - Before marking pod as ready, K8S hits endpoint. Until it returns success, pod wont receive traffic.

<img width="581" height="177" alt="image" src="https://github.com/user-attachments/assets/e341ba4e-647d-4237-95a0-fa04e9d158d8" />


--------------------------------------------------

Suppose you have deployed an application and its failing with error in readiness probe of pod. How will you debug the issue?
-
- If we've deployed app ut pod status is 0/1 ready with readiness probe failed, this means pod isnt receiving any traffic as readiness probe keeps failing

- To debug
  - Check pod  status and events using get and describe commands. Look for readiness probe failure messages
  - Check if connection refused, timeout or HTTP 500 is there
  - Verify app logs using logs command, Check for if app has actually started, if its listening on expected port, if there are startup errors
  - Verify probe config in deployment file. Check if endpoint is valid, port is valid, initialDelaySeconds is enough

- To check manually, exec into pod and test endpoint URL :- exec command and then curl http://localhost:8000/ready
  - If it returns 404, connection refused means app not listening on port, timeout means app not ready yet, HTTP 500 means internal server error
 
--------------------------------------------------

Can you explain how does  service maps incoming calls to particular pod or app?
- 
- K8S service provides stable endpoint (DNS name and IP) to access pods.
- As pods are ephemeral, services abstract away pod IPs and provide LB across matching pods
- We can use clusterIP, nodePort, LB depending on app needs

- For routing calls to pod
  - Use labels and selctors to determine pods belong to which service
  - Use kube-proxy to do LB of traffic across pods in service. If pod fails, its removed from endpoint so no traffic is sent to it
  - If pod fails readiness probes, its removed from service endpoints and new pod gets added as it pass readiness checks. This ensures healthy pods get traffic
  - Optionally we can use Ingress for external traffic. Traffic enters cluster then IC routes specific service requests to the designated pods
 
--------------------------------------------------

What will happen if labels and selectors dont match?
-
- We define service with metadata and selector same as deployment labels

- If they dont match :-
  - K8S cannot associate any pods with service
  - If client tries to access service, request goes to ClusterIP but kube-proxy has no pods to forward it to. So connection will hang or fail
  - K8S doesnt auto match or correct label
 
- To fix this, either update pod labels or update service selector
- After fixing, check endpoints again

--------------------------------------------------

If we dont specify replicas in deployment manifest, what will happen?
-
- K8S defaultly creates one replica
- If only 1 pod is there, no HA. LB is also not performed all traffic will be routed to one pod only
- If we have to scale pod, we've to scale manually

--------------------------------------------------

Can you explain difference between external and internal service?
-
- Service exposes set of pods to enable network access

- Internal
  - Accessible only within K8S cluster. Typically used for pod to pod communication
  - Uses clusterIP mode
  - Useful for backend services, DBs or internal APIs
 
- External
  - Accessible from outside K8S cluster. Traffic reaches service from outside cluster
  - Exposes app to internet or external systems. 
  - Uses Nodeport or LB type. Node port exposes static port on all worker nodes
  - Creates cloud LB pointing to pods
 
--------------------------------------------------

Can a service of type ClusterIP access pod which is on different cluster?
-
- Cluster IP service cannot access pod in different K8S cluster
- It exposes service only inside cluster
- It is not routable outside of cluster. Traffic is limited to cluster's internal network

- Each cluster has its own oisolated network NS. Pod IPs are internal to cluster
- There is no routing between clusters by default. Request sent from one cluster to another fails at network layer

--------------------------------------------------

What kind of automations have you done in your projects?
-
- Frequent question
- Automating terraform infrastructure
- Automated shell scripts for day to day uses
- Talk about how monitoring is enabled in projects with datadog, splunk

--------------------------------------------------

For a production application, what all metrics will you setup for that application?
-
- Frequent question
- Setup SLI, SLA, SLO in first place
- For metrics use things like error rates, latency rate, response times, request counts for requests
- Also check resources metrics like CPU, memory, network I/O flow etc
- Enable audit logging for users using centralized logging
- For any app issues, access issues setup alerts or triggers

--------------------------------------------------

What is the difference between average and percentile?
-
- Average is measn where sum of all values divided by no of values. Use for response times of 5 requests
- Percentile define value elow which certain % of data points fall
  - Sort all values in ascending and then find out percentile
  - Used to understand latency distribution SLA and SLOs
  - For reponse times of 100, 120, 200, 500, 1000. 50th percentile is 200ms as 50% of requests are <= 200ms. 95th percentile means 95% requests are <= 1000ms
  - Percentile shows worst case latency that most users experience
 
--------------------------------------------------

Can you explain 400 and 500 HTTP codes?
-
- 400 series are client errors. Request made by client is invalid or cannot be processed.
  - 400 is bad request
  - 401 is unauthorized/invalid auth token
  - 403 forbidden. Authenticated but not allowed to access resource
  - 404 Not found, requesting resource that doesnt exist
 
- 500 series are server errors. Request reached to server but server failed to process it due to error
  - 500 internal server error, null pointer exception
  - 502 bad gateway, reverse proxy receives invalid response from backend
  - 503 service unavaileble, server under maintenance
  - 504 gateway timeout, reverse proxy times out waiting for upstream
 
--------------------------------------------------

Issue is reported by developer docker application was working on local. But when he tries to deploy on K8S cluster it throws connection reset error from database. How to check and debug?
-
- This usually points to network, service or config issues in K8S
- Check pod logs to confirm exact error, note DB host, creds used in app
- Check DB parameters like DB hostname, port, username and password are correct
- Test network connectivity from pod. Exec into pod and try to reach DB using curl. If fails network or DNS issue is there
- Check service endpoint ensure service exist and endpoints point to healthy pods
- Check DB firewall and SG
- Verify DNS resolution for services and hostnames is correct
- Check resource limits

--------------------------------------------------

# ✅ L2 Equifax Managerial - 15 Minutes

Behavorial questions asked
