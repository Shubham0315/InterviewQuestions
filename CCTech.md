# ✅ CCTech Technical

Write shell script to find factorial of 5?
-

<img width="702" height="541" alt="image" src="https://github.com/user-attachments/assets/118e3c3a-0ef7-407a-aac9-d737d8f7c8fe" />

--------------------------------------------------

How have you integrated Security tool like sonarqube with application ?
-
- Setup SQ server - Installed SQ and configured necessary projects and quality profile/rules
- CICD Integration :- Configured our jenkins pipeline to include SQ scanning as stage in build process. Added SQ plugin in jenkins and passed necessary env variables like SONAR_HOST_URL and auth token
- Code analysis :- During build, code is analysed using sonar scanner. Scanner uploads results to SQ server including metrics like bugs, vulnerabilities, code smells, duplications and coverage
- Quality gates :- Configured Quality gates in SQ to block builds if critical bulnerabilities are found
- Reporting and feedback :- Developers receive immediate feedback on issues so they can fix before prod deployment

--------------------------------------------------

If there is any vulnerability in code, what is the approach to resolve?
-
- First I would review the vulnerability report to understand type, severity and affected code
- Ask dev to resolve the critical issues on priority and follow secure coding standards
- Once fixed, test the code and perform security checks again to ensure it doesnt break any functionality performing peer code review
- Deploy code on test using pipelines
- Use quality gates in SQ to fail the build if new vulnerabilities are introduced

--------------------------------------------------

Which tool do you use for logging?
-
- Cloudwatch logs allow us to collect, monitor and store log files from AWS resources and apps
- Logs are stored in log groups and each log group contains multiple log streams

- We can get logs from sources like EC2, AWS srevices
- We can also setup real time monitoring along with alarms using SNS notifications

--------------------------------------------------

What are the types of pipelines in Jenkins? Write declarative synatx with common stages
-
- Declarative
  - Structured synatx. We define code inside pipeline block. Written in yml
  - We can have stages inside which we've steps to be perfoemed for each stage
  - Easy to read, maintain
 
<img width="737" height="617" alt="image" src="https://github.com/user-attachments/assets/4ea90111-942c-427f-9404-dd27819ba806" />

- Scripted
  - Programmatic and flexible synatx. Allows complex logic, loops, conditionals
  - Harder to read and maintain compared to declarative
  - Written inside node block
 
<img width="720" height="302" alt="image" src="https://github.com/user-attachments/assets/d0ba0f85-258b-4049-bc73-3f1f8e14e8e4" />

--------------------------------------------------

How to pass credentials inside jenkins pipeline?
-
- Use jenkins credentials plugin to store sensitive info in jenkins creds like username, password, SSH keys in credentials manager
- Access credentials inside pipeline using withCredentials block

<img width="761" height="313" alt="image" src="https://github.com/user-attachments/assets/0005288b-7918-41da-be2b-d10b685c7c98" />

- Never hardcode passwords or secrets in jenkinsfile and avoid printing secrets in console output

--------------------------------------------------

Write stage for building docker image and pushing it to ECR using jenkinsfile
-
- First configure AWS CLI in Jenkins agent and store AWS creds
- Create any ECR repo

- Define region, ECR repo and image tag in environment block of pipeline
- In stages define build and push stage where call AWS creds using withCredentials block.
  - Write script to login to AWS, then build image, tag and the push to ECR
 
<img width="760" height="709" alt="image" src="https://github.com/user-attachments/assets/fbeab106-c9aa-4d91-97d7-817af7f36bfb" />


--------------------------------------------------

Write a simple docker file to containerize a nodejs app
-

<img width="736" height="503" alt="image" src="https://github.com/user-attachments/assets/de9ffd59-0155-4cac-b44c-cdf8a1ddf90a" />

- Copies package.json and package-lock.json first for caching npm install
- npm install installs dependencies

--------------------------------------------------

Write a simple docker file to containerize a java app
-

<img width="735" height="352" alt="image" src="https://github.com/user-attachments/assets/8f69bacd-381e-48a1-adf2-d92e89d153c0" />

- COPY target/myapp.jar app.jar → Copies the compiled JAR file from your local target folder.
- ENTRYPOINT ["java", "-jar", "app.jar"] → Runs the JAR when the container starts.

--------------------------------------------------

