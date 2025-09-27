# ✅ L1 Spryc Technical (11 Questions) 29 Minutes

Can you explain Role based access control with respect to IAM?
-
- RBAC is a way to manae access to resources based on roles rather than individual users
- We can assign roles to users/groups.

- User represent person or service that interact with AWS
- Group is a collection of users, we can attach policies to groups and users under the group gets those policies
- Role provides temporary permissions to communicate between services. Also used for external users access
- Policy is JSON document that define allowed or denied actions on resources

-------------------------------------

What is 3 tier application ?
-
- Its common architectural pattern in software development where app is divided into 3 logical layers (tiers) each with specific responsibility

- Presentation Tier - Frontend
  - User interface layer which handles displaying data and receiving user input
  - Communicates with app tier to fetch or send data
 
- Application Tier - Business logic or Backend
  - Handles business rules, processing
  - Receives input from presentation tier, processes it and interacts with DB tier
  - Can handle authentication, logging, validation
 
- Database Tier - Data Layer
  - To store and retrieve data
  - Communicates only wit app tier
  - Ensures data integrity, transactions and persistence
 
- User submits login → frontend sends credentials → backend checks DB → returns success/failure → frontend displays result

 -------------------------------------

 Where the application is hosted and how SGs are helping to restrict access on the app?
 -
 - Frontend - EC2, ALB and S3. Users can access via HTTP/S
 - Backend :- EC2, lambda. Handles business logic and API endpoints
 - Database :- RDS, DyanmoDB. Only accessible by app layer, not usually public

 - Fronedend SG allows inbound traffic on port 80/443 from internet. Outbound traffic allowed to app tier SF
 - Application SG allows inbound traffic only from frontend SG and outbound traffic to DB SG
 - DB SG allows inbound traffic only from app SG and no public access allowed

-------------------------------------



