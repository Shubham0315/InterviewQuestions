# ✅ HSBC Technical (11 Questions)  42 Minutes

If we're deploying image on EKS cluster, I want to split that image name with colon with string variable in pipeline, how to do that?
If we've multiple images wich we need to deploy on EKS cluster one by one using string variable inside pipeline, how to do that?
-
- We can use environment block inside our pipeline to define env variables.
- Values of those variables we can call in our deploy stage of pipeline

- Suppose we've 2 vars IMAGE_NAME and IMAGE_TAG
  - To split we can :-   **echo "Deploying ${imageName}:${imageTag}"**
 
- To have multiple images deployed one by one, put images in environment block.
- Inside stages define images as split and use for loop to deploy one by one

<img width="763" height="654" alt="image" src="https://github.com/user-attachments/assets/1db577df-ac00-4ae4-a389-f24ad5e44088" />

 
-----------------------------------------------

Suppose developer writes code in GitHub repo which we need to checkout and deploy on EKS cluster. What pipelines steps are required?
-
- Inside jenkins pipeline set env variables like AWS_REGION, NS, EKS_cLUSTER, APP_NAME, IMAGE_NAME and then define stages for pipeline deployment

<img width="746" height="317" alt="image" src="https://github.com/user-attachments/assets/6275e7cf-9a6d-41ae-aefc-504fc7ed2f93" />

- First is code checkout from SCR

<img width="749" height="190" alt="image" src="https://github.com/user-attachments/assets/397f1f5a-1701-4638-8cfd-bbdce1f6bcd2" />

- Then build docker image and login to ECR
- After which push image with APP_NAME/IMAGE_TAG to ECR_REPO/IMAGE_TAG
- Here connecting to ECR is very imp stage for authentication

<img width="754" height="748" alt="image" src="https://github.com/user-attachments/assets/6c64563f-4851-4104-9b75-3f588eab05cf" />

- Then to deploy on EKS Cluster, configure kubectl for EKS, then set image for deployment. Also define rollout status, wait for it to complete

<img width="778" height="410" alt="image" src="https://github.com/user-attachments/assets/9467f449-839e-4e48-9aca-8c599bf969d5" />

-----------------------------------------------

What is the difference between Scripted and declarative pipeline?
-

-----------------------------------------------

If you have 2 namespaces, how to ensure particular app is deployed to specific namespace only? State command
-
- Command :- **kubectl apply -f deployment.yml -n $NS**
- Alternatively define the namespace name inside deployment file only

- If we dont define NS, it goes to default NS

-----------------------------------------------

If you have deployed app on K8S and pod is not coming up, how will you debug the issue?
-
- Check pod status. Look for pending, CrashLoopBackoff, ImagePullBackoff :- **kubectl get pods**
- Describe pod to check events at bottom like memory issue, failedScheduling :- **kubectl describe pod $name**
- Check logs :- **kubectl logs $name**
- Check events in NS
- Check resource limits if OOMKilled error
- Check if node has enough resources :- **kubectl describe node $node**

-----------------------------------------------

I need to check what all reasources are created on terraform, how to check?
-
- Check terraform state file. Use command to list resources :- **terraform state list**
- To check particular resource :- **terraform state show $resource**

-----------------------------------------------

If you run terraform script and someone is also running same script at same time with some changes, how will terraform behave?
-
- If no state locking is there, tfstate file is updated by both users concurrently which causes overwriting of resources
- We can use State file locking mechanism here with dynamoDB and S3. Here remote state file is maintained in S3 and if 2 users try to apply config, one user will get error acquiring state lock as he need to wait until execution of another user gets completed
- Use remote backend as S3 and define terraform lock inside it

<img width="685" height="155" alt="image" src="https://github.com/user-attachments/assets/774a9a42-719d-4320-a6d8-0247f8ddaff6" />

-----------------------------------------------

What does terraform init do?
-
- Initialize the backend
- Download provider plugins mentioned in main.tf file
- prepares your working directory by setting up backend, downloading providers, and pulling modules — so Terraform is ready to plan/apply.

-----------------------------------------------

If I have to check what all modules, resources created in AWS without browsing through state file, how can we do it?
-
- Command to list all resources :- **terraform state list**
- Command to check details of resource :- **terraform state show module**

-----------------------------------------------

Terraform versions keep changing. I wrote code when it was 1.1 and now its 1.6. For 1.1 I had tested with provider versions of AWS 5.75. If I have those version dependencies based on which I have written code, how can I ensure that my code runs with terraform version 1.1 and AWS 5.75?
-
- Pin the terraform version inside code of main.tf or pin provider version

<img width="765" height="696" alt="image" src="https://github.com/user-attachments/assets/eb1ff53c-5330-4a9c-9a78-7268ede13aea" />

-----------------------------------------------

In terraform init, in case if we dont specify anything the backend is local where state file get created. To create S3 as backend, how can we do that?
-
- We can use backend.tf file where we define where to create remote backend like S3 bucket along with lock as dynamoDB

<img width="672" height="235" alt="image" src="https://github.com/user-attachments/assets/3eb46436-3433-4e54-997a-61891f93e348" />

-----------------------------------------------



