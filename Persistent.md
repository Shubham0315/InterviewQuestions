# ✅ L1 Persistent Technical (5 Questions) 15 Minutes

Can you tell me any interesting work you did lately? What was the solution and your role involvement?
-
- Frequent question depending on work experience
- Can explain 1-2 projects where automation was in place
- Setting up CICD pipeline instead of manual deployment on servers or Linux VMs
- Terraform infrastructure automation of CLOUD

--------------------------------------

Suppose your disk usage is very high and you want to see which directories or files are consuming more space. Which command will you use?
-
- du command
- To check disk usage in current directory :- **du -sh ***
- To find top space consuming directories :- **du -ah /path | sort -rh | head -n 10**
- To get disk usage of directory :- **du -sh /var/log**

- To check for entire filesystem and all mount points :- **df -h**

--------------------------------------

If you have to write shell script to monitor CPU usage which gives you alert when 80% is breached, how will you write that?
-
- Frequent shell scripting question
- Use threshold condition and mailing list where to send alerts
- DU=$(df -h | awk '{print $5} | tr -d %')
- mail -s "Disk space alert" $TO  

<img width="851" height="286" alt="image" src="https://github.com/user-attachments/assets/d3db82e0-6891-43bc-9b3c-58a453684368" />

--------------------------------------

You need to automate user creation in active directory in shell. How would you create it?
-
- On linux we need ldapadd / ldappasswd commands
- First install LDAP utilities :- **sudo apt install ldap-utils**
- Write below shell script having LDAP server details, input file and logic

<img width="760" height="731" alt="image" src="https://github.com/user-attachments/assets/35c183ac-b0e1-4a5d-b2c3-9f2d0e355a5a" />

- Run script :- **chmod +x users.sh** and then **./users.sh**

--------------------------------------

While using docker, container gets restarted and we need to have data within containers. How can we do that?
-
- Containers are ephemeral — if you remove or restart a container, data in /var/lib/docker/containers/... is lost.
- Use docker volumes. When we create volume and bind container to it, the data of container gets bound to host directory as well so even if container restarts we can push local data to new container
- Use bind mounts to host directory directly into container. We dont directly bind host folder to container folder but we create logical partition on container filesystem and data persist within it
- Volumes are for persistent volumes and prod env
- Bind mounts for Direct link to host files/folders for dev env

--------------------------------------


