Your pod keeps getting stuck in CrashLoopBackOff, but logs show no errors. How would you approach debugging and resolution?
-
- Check pod status. Look for restart count, events, OOMKilled
- Check container exit code. 137 for OOM, 1 is general error, 126 is command or permission issue
- Check probes
- Check resource limits
- Check init containers

- To debug
  - Check mounted volumes, check env vars
  - Adjust command, entrypoint if incorrect
  - Increase resources if OOMKilled

--------------------------------------------------------------

You have a StatefulSet deployed with persistent volumes, and one of the pods is not recreating properly after deletion. What could be the reasons, and how do you fix it without data loss?
-
- Make sure PVC exist and bound to correct PV
- Check volume attachments. For cloud volumes, verify volume is detached from previous node
- Delete pod only. K8S will recreate it and reattach existing PVC, preserving data
- If pod cannot schedule check node selector, tolerations, affinity
- Avoid deleting PVCs or data will be lost

--------------------------------------------------------------

Your cluster autoscaler is not scaling up even though pods are in Pending state. What would you investigate?
-
- This means K8S cannot schedule pods due to some constraint or misconfig.
- Check pod events, describe pod. Look for failedScheduling due to resource issues, node selection issues
- Verify cluster autoscalar logs
- Check node group configs if min max size is correctly configured, correct instance types matching pod resource requests, right labels if pod has selector/affinity.
- Check pod constraints like resources, selector, taints
- Verify cluster autoscaler permissions like read node groups, create new nodes

--------------------------------------------------------------

A network policy is blocking traffic between services in different namespaces. How would you design and debug the policy to allow only specific communication paths?
-
- By deafult all pods can communicate with each other across namespaces, cluster-wide
- Once we create network policies in NS, it restricts traffic based on rules like podSelector, ingress, egress

- Here allow ingress from 1st to 2nd service

- To debug check applied network policies, verify CNI plugins supports our network policies

--------------------------------------------------------------


    
