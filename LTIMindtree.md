# ✅ L1 LTI Mindtree Technical (25 Questions) 30 Minutes

Explain the Kubernetes Architecture
-
- **Master Node - Control Plane**
  - It is the brain of K8S.
  - Api Server :- User facing component of K8S to take all requests and process them after validation
  - etcd :- backup store of K8S where info of the cluster is stored in the form of key value pairs
  - scheduler :- Schedules the requests received by api server on worker nodes. Decides on which node resources to be scheduled
  - Controller manager :- runs controllers that continuously monitors cluster state and ensure desired state is maintained. Node controller monitors node health, RSController ensures right no of pods running
 
- **Worker Node - Data Plane**
  - Machines or VM that run actual application workloads
  - Kubelet :- Running agent on every node. Ensures containers are running as instructed by API server
  - kube proxy :- Handles networking and load balancing of pods. Generates IP address for pods
  - Container runtime :- Software actually responsible for running containers on nodes
 
--------------------------------------------------------------------------------------------------

On EKS we've deployed 2 services nginx and tomcat. Both having deployment and service components. How nginx deployment will know it should connect to respective nginx service and similarly for tomcat?
-
- In EKS or K8S cluster, services act as stable network endpoints to route traffic to appropriate set of pod/deployments
- Deployment knows how to connect to its service is based on DNS service discovery

- Here we can use labels and selectors concept in yml files. Define labels in deployment.yml file and also add tag of selector to ensure desired replicas are created if the label name matches selector. Define selector in service.yml file. So if the label in deployment file matches with selector of service, they will be connected to each other
- To expose app to external world, create service keeping same selector as deployment so the relevant pods are connected
- Thus nginx deployment pods are discovered by nginx service

- So here K8S auto creates DNS entry for each service
- To connect nginx deployment to nginx service, connect to DNS name nginx so K8S DNS will resolve nginx to clusterIP of service and route traffic to pods
- In short Kubernetes DNS ensures nginx-service resolves to nginx Pods, and tomcat-service resolves to tomcat Pods.

--------------------------------------------------------------------------------------------------

Suppose you have a pod and you want to restrict it going into some of the worker nodes, what will you do?
-
- We can use node selector and node affinity - Node selection rules
- Node selector :- If our pods have limitation to work only on windows machine or node, node selector if defined in yml file tries to find the exact node to schedule the pod matching 100% requirement and schedules pod accordingly
- Node affinity :- It has 2 options required and preferred. It tries to find exact node match and if not found it schedules our pod on other nodes
- Node selector does hard selection and node affinity does soft selection

<img width="752" height="284" alt="image" src="https://github.com/user-attachments/assets/32cf23f1-7c75-4076-af60-ba108b0fa06a" />

<img width="744" height="454" alt="image" src="https://github.com/user-attachments/assets/3a46ec86-e5c8-40ad-b99b-8c13d840a3ee" />

- We can also use taints and tolerations here

--------------------------------------------------------------------------------------------------

What is the key difference between deployments and stateful sets?
-
- Deployments are used for stateless apps, treating all pods as identical and interchangeable meaning doesnt matter which pod serves request. They get randon names after creation. We can scale up/down them in any order
- Statefulset is K8S API workload objects dealing with stateful apps in K8S. Stateful apps means which require stable network identity, ordered deployment/scaling, persistent volumes. Here pods have fixed identity (no random names). Each pod can have its own PVC that sticks with it even if pod restarts

--------------------------------------------------------------------------------------------------

How to pass env variables to deployment?
-
- We can define env vars directly into pod yml file by hardcoding. Its simple but not secure

<img width="742" height="591" alt="image" src="https://github.com/user-attachments/assets/1f059221-1165-4eaa-a82e-5489536cf405" />

- We can use configMaps to keep non-sensitive values outside yml file. Create config map and use in yml file as reference

<img width="753" height="340" alt="image" src="https://github.com/user-attachments/assets/0524639b-912f-44f8-b276-fb7553a2e8c9" />

- We can use secret for sensitive data. Create secret and pass in yml files

<img width="763" height="350" alt="image" src="https://github.com/user-attachments/assets/e456a5ac-43ca-4d23-9596-c5ce4e2edf15" />

--------------------------------------------------------------------------------------------------

If you have some files into local or jump server and you've to connect to EKS cluster and copy files to PODS in EKS , how to copy those files?
-
- First connect to EKS cluster to make sure kubectl can talk to EKS
- Copy files to pod :- kubectl cp localPath namespace/podName:containerPath

- If files are on jump server which has access to EKS, first copy files to jump server and then copy to EKS

- We can also mount them via configMap, secret or PV like EBS

--------------------------------------------------------------------------------------------------

If we delete the pod, the copied content bound to it will be deleted or not?
-
- When we copy the files into pod, wthey get stored in container's filesystem which is ephemeral. If pod is deleted or recreated, copied files are lost
- To persist files across pod deletion/recreation, we need PV and PVCs. Here when we copy files to directory, they will stay even if pod is deleted as PVC is bound to EBS

<img width="754" height="212" alt="image" src="https://github.com/user-attachments/assets/9e629f2c-4c2a-47bd-8bb7-4c0bfe293f48" />

--------------------------------------------------------------------------------------------------

What is init container?
-
- Special type of container that runs before main application containers in pod are started
- They're used to run setup tasks like download files, wait for dependencies before app start. Run sequentially in order
- They must complete successfully before app container start
e.g :- Wait for DB or service to become available, initialize config files, pull secrets and binaries before running app

--------------------------------------------------------------------------------------------------

What is daemon sets?
-
- Its a K8S controller that ensures a copy of pod runs on every node in cluster
- Its one pod per node
- When new nodes are added to cluster, pods are auto scheduled there. Same for removal
- Typically used for background system tasks or node level services

--------------------------------------------------------------------------------------------------

You have a pod for which you want to restrict access on other K8S objects, how to control that in K8S?
-
- By default all pods run under default ServiceAccount in their namespace and that serviceaccount might have no restrictions
- If we want our pods to not access othe K8S objects we can use RBAC with dedicated service account

- First create SVC Account 

<img width="753" height="187" alt="image" src="https://github.com/user-attachments/assets/bc57f10b-3a27-4d19-83ce-c1e3c1fcd485" />

- Create role with limited permissions
- Bind role to SVC Account. Without binding SVC account has zero permissions
- Attach SVC account to pod

<img width="754" height="264" alt="image" src="https://github.com/user-attachments/assets/40e5e643-d2cd-4491-88d1-a69a8635283a" />

--------------------------------------------------------------------------------------------------

Can you horizontally scale the pod based on CPU/Memory metrics?
-
- Using HPA

--------------------------------------------------------------------------------------------------

How to expose our microservices to external world?
-
- Using LB service or NodePort service
- Also we can use Ingress and controller. Ingress to define HTTP/HTTPS routing. Instead of creating 1 LB per service, we can expose many services via one LB
- Also SG and NACL method

--------------------------------------------------------------------------------------------------

What is the branching strategy you use in project?
-
- Standard branching strategies help us manage source code, releases and features efficiently.
- We use git flow branching where we have dev, prod, feature, release, hotfic branches
- We also use release or hotifix strategy

--------------------------------------------------------------------------------------------------

What are stages in your Jenkins pipeline?
-
- checkout, build, SonarQube, nexus, deploy to prod
- We initially were creating diff jobs for each stage. But now we use single pipeline

--------------------------------------------------------------------------------------------------

You might have integrated Jenkins with your EKS cluster. Where you will be adding cluster details like API server endpoints, secrets in Jenkins?
-
- When integrating jenkins with EKS, we need to give jenkins access to our K8S API
- Jenkins credentials store :- add API server token in here. In jenkins pipeline refer them using withCredentials block. It keeps sensitive data out of jenkins file and provides secured, centralized storage with access controls
- K8S Plugin in Jenkins :- Manage jeniins - configure cloud - add new cloud - K8S. Add API server endpoint

--------------------------------------------------------------------------------------------------

What are the best practices for reducing docker image size?
-
- Reducing image size allows faster build and deployments, low storage costs, smaller attack surface
- We can use small base images instead of eavy OS. Use distorless images
- Use multi stage docker builds so only required artifacts from previous stages are copied to next stage to have minimal runtime
- Use AS Builder and --from=Builder

<img width="739" height="352" alt="image" src="https://github.com/user-attachments/assets/c5dcfb68-3c3f-4b6b-9f7e-e3ac8f8f3ad9" />

- Remove unwanted packages, scan and optimize regularly

--------------------------------------------------------------------------------------------------

How are you maintaining your terraform state file?
-
- Using AWS S3 as remote backend. So whatever changes we apply are updated in statefile as well

--------------------------------------------------------------------------------------------------

In Jenkins suppose there is an user who needs access to specific folder, how to achieve that? Which plugin is used?
-
- RBAC Plugin :- Create roles at global level or folder/project level. Assign roles to users/groups
- Matrix based auth plugin :- more granular permissions but not as clean as folder level permissions

--------------------------------------------------------------------------------------------------

Consider in one VPC you have EKS and in another VPC you have jump server. How the connectivity will occur?
-
- VPC peering
- Create peering between both VPC. Update route tables in both allowing traffic between CIDR ranges
- Update SGs allowing inbound from jump to EKS and vice versa allowing outbound traffic

- If the EKS API is private, peering ensures private access; if it’s public, the jump server can connect directly using IAM-authenticated kubeconfig.

--------------------------------------------------------------------------------------------------

What is DDOS attck?
-
- Distributed denial of Service
- When large no of compromised machines flood target system with traffic, overwhelming its resources and making it unavailable for legitimate users
- Types :- Volume based, protocol attck, app layer attack
- To mitigate use network level protections like CDN/WAF, app level protections like circuit breakers, auto scaling, deploy apps to multiple AZs, use LBs, use auto scaling

--------------------------------------------------------------------------------------------------

What you are doing for DR strategy in your current project?
-
- In current project we're building app on AWS. As this involves highly sensitive data , we follow strong DR strategy
- Use multi AZ HA :- Our EKS cluster nodes are spread across multiple AZ
- Automated backups wit daily snapshots. App state stored in PV is regularly backed up to S3
- IaC for fast recovery :- All infra defined with terraform
- Monitoring and drilles using cloudwatch
- Using blue-green deployments

--------------------------------------------------------------------------------------------------

State difference between SG and NACL
-

--------------------------------------------------------------------------------------------------

How will you define subnet whether its public or private? State difference
-

--------------------------------------------------------------------------------------------------

How are you taking backups of K8S PV?
-
- We can take volume snapshots of PV
- We can do using EBS snapshots and volumes.
- We can automate cron jobs or lambda functions to have same
- To store them use S3 or an other storage

--------------------------------------------------------------------------------------------------

How you can take backup of instance or block volumes in AWS?
-
- Instance :- Using AMI wich is snapshot of instance OS and attached volumes
- EBS lock volumes :- Using snapshot wich is point in time copy of volume stored in S3. Can be manual or automated via policies

--------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------


# ✅ L2 LTI Mindtree L2 Managerial (23 Questions) 36 Minutes


What are the the specific activities which you do using kubernetes in your current role?
-
- In my current role, we're acticely using EKS for application deployments, scaling, monitoring and security
- Application deployment and management (writing manifests)
  - Creating and managing deployments, stateful sets, daemon sets for various microservices.
  - Exposing services to world using clusterIP, nodeport and LB
- Managing config maps and secrets to inject environment variables, DB creds and API tokens into pods
- Creating and attaching PV like EBS and PVCs. Implementing snapshot based backup
- Security and access control using RBAC to restrict pod/service access
  - Applying network policies to limit pod to pod communication
- Configuring HPA and autoscaler. Distributing workloads across multiple AZs
- Integrated K8S deployments with jenkins pipelines using kubectl
- Managing ingress controllers for external traffic routing
- Integrate K8S resources with monitoring tools like datadog

--------------------------------------------------------------------------------------------------

Have you created any K8S cluster?
-
- Yes I've created K8S Clusters in AWS using EKS with terraform. I've set up VPCs, subnets, SGs, node groups and configured kubectl access
- In my current role, for testing I've also setup cluster using minikube on Linux VMs

--------------------------------------------------------------------------------------------------

Which kind of storage your app team is using for K8S cluster?
-
- In our current setup on EKS, we're using different storage options based on workload type
- **AWS EBS backed PV/PVC**
  - Used for most stateful workloads like DBs, MQs and apps requiring persistent volumes
  - We provision PV dynamically via storage class like gp2 or gp3
  - App pods use PVC. PVCs requests storage, K8S dynamically provisions an EBS volume and mount it to pod
 
<img width="756" height="315" alt="image" src="https://github.com/user-attachments/assets/54e6f82c-8dd0-4bab-95b3-d74879ae6a87" />

- **AWS EFS backed PV/PVC**
  - When multiple pods need shared storage, we use EFS
  - PVC requests readwritemany access which only EFS supports
 
<img width="759" height="333" alt="image" src="https://github.com/user-attachments/assets/0ebd8f34-85ff-4846-810b-ffc53a84a65f" />

- Ephemeral / Empty dir (no PV/PVC)
  - For stateless services where no PVC needed, vanishes when pod is deleted
 
--------------------------------------------------------------------------------------------------

How many types of volumes we can create in K8S cluster?
-
- Ephemeral volumes (temporary), persistent volumes (backed by external storage)
- Ephemeral :- emptyDir, configMap, secrets. Used by stateless microservices
- Persistent :- EBS, EFS which usually requires both PV and PVCs
- Special volumes :- PVC binding to PV and hostPath which mounts file/directory from node's filesystem into pod
- For object storage needs, we use S3 as well

--------------------------------------------------------------------------------------------------

If your app team wants to utilise file storage EFS, how we can integrate in K8S cluster?
-
- We'll create EFS file system in AWS in same VPC as of EKS cluster, attaching subnets in each AZ where worker nodes are running
- We'll create storageClass for EFS which tells K8S how to provision PVCs on EFS
- Then we'll create PV for EFS and then will create PVC to request storage
- Then mount PVC to pod allowing multiple pods across AZs to share files (RWMany)

- To match PV with PVCs
  - When we create PVC, k8s tries to find match of suitable PV. This is done based on some rules

--------------------------------------------------------------------------------------------------

How to match PVC request with PV in K8S cluster?
-
- To match PV with PVCs
  - When we create PVC, k8s tries to find match of suitable PV. This is done based on some rules
  - **Storage class** :- PVC specifies storageClassName, it will bind to PV with that name only. If PVC doesnt define storageClassName K8S dynamically provisions default storageClass
  - **Access Modes** :- PVC access modes must be subset of PV access modes (ReadWriteOnce)
  - **Capacity** :- PVs storage capacity must be >= PVC request
  - **Selector** :- Optional to use
 
--------------------------------------------------------------------------------------------------

If you create role in K8S, how will you bind that role to service account?
-
- Create serviceAccount
- Create role for permissions within namespace

<img width="739" height="257" alt="image" src="https://github.com/user-attachments/assets/0cc08238-fb74-4e82-8899-092c41625186" />

- Bind role to service account using role binding

<img width="752" height="320" alt="image" src="https://github.com/user-attachments/assets/4ab3c4a0-43c5-4650-81ae-d4a356c954a5" />

- Use service account in pod yml

<img width="718" height="259" alt="image" src="https://github.com/user-attachments/assets/c8430fe3-5b91-40a8-9737-b9b259a0a1d0" />

--------------------------------------------------------------------------------------------------

How will you bind the AWS IAM role to service account in K8S? How that service account will authenticate ith AWS role?
-
- Here we can use IAM Roles for Service Accounts (IRSA). It allows to bind IAM role to K8S SVC Account so pods can securely call AWS APIs without static IAM keys

- Create IAM policy
- Create IAM role with trust policy
- Create service account annotated with IAM role
- Use service account inside pod

<img width="811" height="744" alt="image" src="https://github.com/user-attachments/assets/07eae24f-5386-445b-bc0a-310e2ed6145e" />

--------------------------------------------------------------------------------------------------

If I have created one PVC which is not getting bounded or volume is not getting created, how to debug this issue? If PVC is in pending state?
-
- Check PVC status. Status could be pending :- **kubectl get pvc**
- Describe PVC for events like no PV available, waiting for volume to be created, storage class not found :- **kubectl describe pvc $name**
- Check storage class if provisioner is correct EBS/EFS :- **kubectl get sc**
- Check PVs to ensure capacity >= request, ensure accessModes match, ensure labels match PVC selector, storage class match
- For EBS check if worker nodes have IAM permisiions to create/manage volumes

--------------------------------------------------------------------------------------------------

What is the difference between static and dynamic PV ?
-
- Static
  - Admin manually creates PV inside cluster
  - PVCs can bind only if tey find matching PV  (access modes, capacity, storage class)
  - Pre creating EBS volumes and then defining PV that points to it, if no PVC matches, PV stays unused
 
- Dynamic
  - Auto creation of PVs when PV is requested
  - PVC references storage class and K8S provisions volume on cloud/storage backend
  - Avoids pe creation
  - Common in cloud
 
--------------------------------------------------------------------------------------------------

My pod got stuck in container creating state, how to debug the issue?
-
- Check pod status. Common errors can be FailedMount, ImagePullBackoff, ErrImagePull, Insufficient resources :- **kubectl get pod**s or **kubectl describe pods $name**
- Check volume mounts if PVC not bound. Storage issues like wrong access modes can cause this
- Check image pull issues
- Check node conditions. If node is not ready it can cause issue and pod gets stuck
- Check if CNI plugin is broken

--------------------------------------------------------------------------------------------------

If pod saying failed to create sandbox, what does that mean?
-
- When pod says this, it means K8S couldnt even set up network sandbox that every pod needs before starting containers
- Pod sandbox are created by kubelet which acts as network namespace for pod. If sandbox not created, no container will run

- This could be due to CNI plugin issues which is used to attach pod to cluster network, can be due to firewalls of SG/NACL, node issues, misconfigured podspec

- First describe pod, check kube-system pods

--------------------------------------------------------------------------------------------------

If pod fails to get IP assigned , what can be done?
-
- Its directly tied to CNI plugin which is responsible to assigning pod IPs and setting up routes
- If CNI plugin is not installed then it happens, also if IP address pool get exhausted, IAM permission issues

- To debug
  - describe pod and look for events saying failed to assign IP
  - Check subnet IP availability
  - Check node status, IAM permissions
 
--------------------------------------------------------------------------------------------------

If in one of the service I am not getting logs, how to debug the issue? Pod is up and running
-
- If service is not showing logs it means either pod isnt writing logs or logging pipeline isnt capturing them
- When we say a pod is not generating logs, it usually means that nothing is being written to stdout or stderr of the container processes.

- Check pod status, ensure its running
- Try fetching logs directly. If empty, maybe app isnt writing stdout/stderr :- kubectl logs $pod
- Check logging agent in EKS its cloudwatch and ensure all pods running
- Check app log path
- Check for permissions/RBAC. If only one service has issue, check log agent permissions

- If a pod is running but I don’t see logs, I first check logs with kubectl logs. If still empty, I exec into the pod to confirm the application is running and check whether it’s writing logs to a file instead of stdout/stderr. Many apps like Tomcat and Nginx log to files, so in that case I’d configure the container to redirect those files to stdout/stderr. If centralized logging is used, I’d also check that the logging agent (FluentD/FluentBit/CloudWatch) is correctly scraping that namespace and log path.

--------------------------------------------------------------------------------------------------

What is the K8S architecture?
-

--------------------------------------------------------------------------------------------------

What is the difference between deployments, stateful sets, replica sets and daemon sets?
-
- Deployment → Deploy stateless.
- StatefulSet → Stateful workloads.
- ReplicaSet → Replication only.
- DaemonSet → One pod per daemon (node).

<img width="1721" height="677" alt="image" src="https://github.com/user-attachments/assets/ae33d984-5a46-4f68-939d-9a47563ac373" />

--------------------------------------------------------------------------------------------------

What is Pod Disruption Budget (PDB)?
-
- A Pod Disruption Budget is a Kubernetes resource that limits how many pods of an application can be voluntarily disrupted at a time. It ensures high availability during maintenance activities, like node upgrades, draining, or scaling down.
- Without PDB, Kubernetes could evict all pods of a deployment during node maintenance, causing downtime. PDB ensures that a minimum number of pods remain available.
- We define min or max available here

--------------------------------------------------------------------------------------------------

What is taints and tolerations?
-
- Taints are applied on Nodes while tolerations are applied on pods
- To update K8S cluster with 3 worker nodes, we cannot do it once due to downtime issue. We drain one node, move all pods on another node, make it non scheduleable. Then bring it down, update it and remove non scheduleable status to make new version running
- Here taints are used which is label or stamp given to node to make it non scheduleable
- 3 types
  - **noSchedule** :- node goes to non scheduling status
  - **noExecute** :- all pods immediately stop working
  - **prefereNoSchedule** :- used when node performance is declining
  - Command :- **kubectl taint nodes $name key=value:effect**(3 above)
 
- Toleration is applied to pod to allow it to be scheduled on nodes with matching taints
- On noSchedule node we can add toleration to pod and run on it
- Scheduler understand pod is there with tolerations still we can schedule resouces on the node

--------------------------------------------------------------------------------------------------

What is node selector and node affinity?
-

--------------------------------------------------------------------------------------------------

What is pod affinity and pod anti affinity? Give me one example
-
- Pod affinity allows pod to be scheduled on same node as other pods that match certain labels
- Used for performance, low latency communication and shared resources
- frontend and backend of app

- Pod anti-affinity ensures pod is not scheduled on same node as other pods taht match certain labels
- Used to spread pods across nodes for HA and fault tolerance
- If we've multiple DB pods and want them on diff nodes to avoid single node failure taking down all replicas

--------------------------------------------------------------------------------------------------

If my container is going into ImagePullBackoff due to authentication issue, how to make sure node download image and make pod up and running?
-
- It means Kubernetes cannot pull the container image from the registry because credentials are missing or incorrect.
- Check for error in failing to pull image (imagePullSecret)
- Create docker registry secret to authenticate to private repos
- Attach secret inside pod.yml
- Delete failing pod and then verify the authentication

--------------------------------------------------------------------------------------------------

My requirement is to create readOnly accesss for my application team, how to do that?
-
- Use RBAC
- Craete role within NS allowing get, list, watch
- Create roleBinding assigning role to user/group/SVC account
- Verify access

--------------------------------------------------------------------------------------------------

How to connect to EKS from jump server/ bastion host?
-
- We need to ensure network access and proper K8S authentication
- Bastion host must have connectivity to EKS cluster, kubectl installed, IAM role with permissions
- Update kubeconfig for EKS cluster which fetches cluster info and generates kubeconfig file at ~/.kube/config
- Verify kubectl connectivity from host

--------------------------------------------------------------------------------------------------

