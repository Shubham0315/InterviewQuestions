# ✅ L1 Fiserv Technical ( Questions)  Minutes

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

