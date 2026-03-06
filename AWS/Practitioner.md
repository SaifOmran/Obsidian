### Cloud deployment types #Video1
1. On-prem (private cloud): The application and workload are on the physical infra.
2. Public cloud: All workloads and apps are on the cloud at any cloud provider.
3. Hybrid: The workload and apps are distributed between On-prem and public cloud.
---
### Cloud benefits #Video2
1. Cost saving
   No need to pay huge upfront costs for the infra and then not using these too much resources, no need to pay for cooling and the electricity.
2. Variable expenses
   pay as you go as there is no upfront costs.
3. Elasticity (Scalability)
   Scale in or scale out the resources when needed.
4. Economies of scale
   AWS has huge amount of resources and too many customers so the prices of the services are good for customers.
5. Speed (Agility)
   Scale in and out in no time.
6. Go global in minutes
   have your own app on internet in minutes.
---
### EC2 #Video3
- Compute server which represents virtual machine.
- Provides secure and resizable compute capacity.
- EC2 instance boots in minutes.
- Pay as you go service, as if the instance is turned off, you will not pay anything.
##### Ec2 instance types
1. General purpose
   provide a balance of compute, memory and networking resources, and can be used for many workloads. These instances are good for applications such as ==web servers, code repositories, and small-to-medium databases.==
2. Compute optimized
   instances are ideal for compute bound applications that benefit from high-performance processors. Some examples of workloads for compute instances are ==batch processing==, media transcoding, and dedicated game servers.
3. Memory optimized
   instances are designed to deliver fast performance for workloads that process large data sets in memory. For example, these instances are good for ==in-memory databases, data analytics, and enterprise applications.==
4. Accelerated computing (GPU)
   instances use hardware accelerators, or co-processors, to perform functions more efficiently. For example, they can perform ==floating point number calculations, graphics processing, or data pattern matching.==
5. Storage optimized
   instances deliver millions of low-latency, ==random IOPS== to applications. They’re designed for workloads that require high, sequential read and write access to very large data sets on local storage. For example, they’re good for ==high-throughput databases, data processing, and data streaming.==
6. High-performance computing (HPC)
   instances offer the best price performance for running HPC workloads at scale. HPC instances are ideal for applications that benefit from ==high-performance processors such as complex simulations, deep learning, and visual effects rendering.==
---
### EC2 pricing #Video4
##### On-Demand
- Pay as you go without upfront payment
- No long term commitment
- Suitable for ==short term==, spike and ==unpredictable== workloads
- Can ==NOT be interrupted== by AWS.
##### Spot instance
- The user uses the unused EC2 capacity with 90% discount
- It can be ==interrupted== by AWS at any time. 
- Used with fault tolerance or stateless workloads or flexible start and end time workloads.
##### Reserved instance (saving plan)
- Reserve instances for 1 or 3 years which will be cheaper than on-demand pricing, and also there is upfront payment.
- Types of saving plans:
	1. ==EC2 saving plans==: limited to instance family and limited to only EC2 service
	2. ==Compute saving plan==: flexibility with EC2 families, and also flexibility with compute services like Lambda
	3. ==SageMaker saving plan==: AI service.
##### Dedicated
1. ==Dedicated instances==: EC2 instances of customer are on isolated host, but the customer can NOT control the physical server and the EC2 instances can run on any physical server but still on isolated host. 
من الاخر : الEC2 instances بتاعتك هتقوم على سيرفر لوحدك بس ملكش دعوة انهي سيرفر ف الداتا سنتر.
2. ==Dedicated host==: The physical server is fully dedicated for the customer (==BYOL==), used with licence migration.
- BYOL = Bring Your Own Licence.
- انت حاجز السيرفر كامل ف الداتا سنتر وليك كل التحكم فيه.. ولو عندك on-prem SQL licence مثلا تقدر تنقلها ع الhost ده من غير ما تشتريها تاني.
---
### ASG #Video5 
- Automatically ==scale out or scale in== the EC2 instance based on policies we define.
- Improve fault tolerance by ==replacing unhealthy instances==.
- يعني بتراقب الinstances ولو واحد failed تضيف مكانه على طول.
- We have to configure some parameters while creating ASG
	1. Minimum number of instances.
	2. Desired number of instances.
	3. Maximum number of instances.
	4. Scaling number -> نزود بالكام واحدة
---
### ELB (Elastic load balancer) #Video6 
- Automatically distribute the load among EC2 instances to improve application performance.
- Acts as single point of contact to all incoming traffic to the backend targets
كل السيرفرات بتكون ورا الload balancer والtraffic بتتجمع عليه وهو يوزعها عليهم.. يعني اليوزر مش بيشوف السيرفرات .. اليوزر بيكلم الload balancer.
- Monitor the health of targets and route the traffic only to the healthy instances.
##### How ASG and ELB integrate with each other ?
لو عندي ASG فيها 3 سيرفرات متوصلة ب ELB وهي دي ال Target group بتاعته وعمال يوزع ال requests عليهم.. ال load زاد وال ASG ضاف 2 instances ساعتها ال ASG بيكلم ال ELB ويقوله زود ال 2 دول في ال Target group بتاعتك ووزع عليهم traffic.
![[Pasted image 20260208011001.png]]

---
### Messaging services (SQS, SNS) #Video7
- Monolithic App: the application components are one unit, so if one component fails, the whole application will fail.
- Microservices App: the application components are separated, so the failing of one component doesn't affect the other components and the application.
##### SQS = Simple Queue Service
- فكرته انه زي buffer بيشيل ال requests عشان ميحصلي overload على components او service معينة في ال application
- مثال: اعتبر application زي Noon بيكون فيه ordering service و shipping service ومفوض ان اي حاجة بيتعملها order بتتبعت لل shipping..لكن عملية ال order سهلة عكس عملية ال shipping اللي فيها خطوات اكتر وتنسيق مع شركات الشحن !! فتخيل لو كل اوردر اتعمل راحت ordering service مكلمه ال shipping service ؟ ساعتها ال shipping service مش هتقدر تواكب السرعة بتاعت ال ordering service وهتقع !! الحل بقا ف ال SQS اللي بتعمل queue بين ال two services وبتجمع فيه ال orders وال shipping service تبص عليه (==polling==) وتنفذ اللي فيه حسب سرعتها هي وكل ما تخلص اوردر تمسحه وتخش ع اللي بعده.. كده انا حافظت ع الshipping service وفي نفس الوقت سيبت ال ordering service شغالة بسرعة زي ما هي.
![[Pasted image 20260208015416.png]]

- SQS: receives, stores and sends message between two components.
- SQS queues the message even the consumer components are not available.
- SQS helps in implementing ==Losse-coupling or De-coupling==.
##### SNS (Simple Notification Service)
- Working with ==Pub/Sub== mechanism.
- There is publisher and topic and some subscribers who subscribing the topic
- When the publisher sends a message to the topic, the message is sent to all subscribers.
- ==Used to send notification, Email or SMS.==
 ---
### Serverless computing (AWS Lambda) #Video8
- نرجع خطوتين لورا للself-managed EC2 instance اللي كان لازم اظبط ال OS واعمل sizing لل resources بتاعتها..كل ده معناه اني بعمل provisioning .. في ال serverless انا مش بعمل provisioning .. انا بس برفع الكود بتاعي وAWS مسئولة تحدد ال resources اللي الكود محتاجها وبتحاسب بال millisecond لما بيحصل trigger للكود عكس ما كنت بتحاسب بالثانية على ال EC2 instance مادام هي up and running حتى لو بت serve شخص واحد.
- ==Run code without provisioning and managing infrastructure.==
- Saves costs as you pay for compute time by ==millisecond==.
- AWS offers up to 1 million free request / month.
- Maximum execution time is ==15 minutes==.
---
### Containers #Video9
##### Why containers ?
- To make the code work on any host as the container contains the code with its dependencies and libraries which are needed to make the code run.
##### What does the orchestration do ?
1. Check available resources on server to run new container.
2. Responsible about the container lifecycle (launch and terminate container).
3. Monitoring the containers health.
### Containers on AWS
##### EKS (Elastic Kubernetes Service)
- Used when I am working ==on prem and want to migrate== to the cloud
1. Manage containers on EC2 instance -> There are some containers on EC2 instance and the EKS managing them.
2. Manage serverless containers ==Fargate== -> Serverless compute engine for containers, used to manage serverless containers.
![[Pasted image 20260208095021.png]]

##### ECS (Elastic Container Service)
- Used when building ==containers from scratch==.
	1. Manage containers on EC2 instance -> There are some containers on EC2 instance and the ECS managing them.
	2. Manage serverless containers ==Fargate== -> Serverless compute engine for containers, used to manage serverless containers.
![[Pasted image 20260208095021.png]]
 > Use Amazon ECS for simple, AWS-native applications, rapid deployment, and lower operational overhead. Use Amazon EKS for complex, large-scale microservices, multi-cloud strategies, or when requiring full Kubernetes ecosystem compatibility. ECS is ideal for smaller teams; EKS is best for teams with deep Kubernetes expertise.

##### ECR (Elastic container Registry)
- Service from AWS that allows developers to easily store, manage, and deploy container images (Alternative to Docker Hub).
- ECR integrates well with AWS services.
- ECR can be private registry for the customer.
---
### AWS global infrastructure #Video10
- ==Availability Zone==: one or more datacentre, which are separated form each other and each one has its power and networking.
- AZs are separated form each others by many kilometres (maximum 100 km). 
- ==Region==: collection of availability zones (minimum 3 AZs).
- Region = 1 country (important for data resilience).
![[Pasted image 20260208102215.png]]

##### Region and AZs naming
- Region = us-east-1 (first region made by AWS by the way).
	- AZ1 = us-east-1a
	- AZ2 = us-east-1b
	- AZ3 = us-east-1c
---
### Region selection criteria #Video11 
1. Compliance -> regulation for governances.
2. Proximity -> Latency
3. Service availability -> Not all service are in all regions. 
4. Cost -> The services cost differs from region to another. 
---
### CloudFront and edge locations #Video12
- تخيل انت في مصر وعايز تتفرج على فيديو على اليوتيوب.. الطبيعي ان ال request بتاعك هيعدي على كل ال routers من مصر لحد سيرفر ال YouTube وده طبعا وقت كبير وlatency عالية.. يجي هنا دور الedge location أو ال cloudfront..دورها بكل بساطة انها بتعمل ==caching== لل content لما اول user يطلبه ف اقرب edge location له بحيث لما user تاني او نفس ال user يطلب نفس الفيديو من يوتيوب يبقى متخزن في ال edge location القريبة دي بدل ما يروح لحد سيرفرات يوتيوب في امريكا مثلا.
- ==CloudFront provides Content Delivery Networks (CDN)==.
- CloudFront speeds up the distribution of static and dynamic web content and image files.
- ==The request is routed to the closest edge location through the AWS backbone network not through the internet infrastructure providing low latency==.
- If the content is not in the edge location (cache miss), Cloudfront retrieves it from the origin, otherwise CloudFront delivers it immediately.
- فين بلاقي ال cloudfront ؟ جوا ال edge location.
---
### AWS outposts #Video13
- AWS Outposts extends AWS infrastructure and services to on-premises environments, allowing customers to run AWS workloads locally while being ==fully managed from the AWS cloud==.
- السيرفر متظبط وجاهز يتوصل كهربا ونتورك عندك في الشركة
- الشركات ممكن تحتاج الservice دي عشان ==low latency and compliance and data residency.==
---
### Local zones and Wavelength zones #Video14
##### Local zones
- AWS Local Zones are an extension of an AWS Region that place compute and storage closer to end users to support ==latency-sensitive workloads==.
- هي extension لل AWS region بس مش زي outpost لان مفيش سيرفر عندي.
- Local zones are in metropolitan cities, which are close to the user.
- Local zones have their own connection to the internet.
##### Wavelength zones
- فكرتها ان ال AWS infrastructure موجودة عند الtelco companies or communication service providers (CSP) والusers بيستخدموها من خلال ال ==5G network==.
- كمان ال traffic من ال user لل servers والعكس مش بتطلع برا ال telecommunication network.
---
### VPC #Video15
- VPC = Virtual Private Cloud, which looking like my virtual datacentre.
- VPC contains subnets which could be private or public.
- The resources in public subnet could be accessed using the their public IP directly from the external users.
- The resources in private subnet are not accessible by the external users.
- مثال عشان الدنيا توضح..تخيل عند VPC فيها private subnet موجود جواها DB servers وفي public subnet فيها web servers..هنا ال user يقدر يبعت للweb server وساعتها الweb server يكلم ال DB server يجيب منها Data..لكن الuser ميقدرش يكلم ال DB server directly لانها في private subnet.
![[Pasted image 20260208211229.png]]

- طيب ازاي ال public subnet بتقدر تطلع internet ؟ عن طريق الinternet gateway (IGW) اللي بيكون على طرف ال VPC.
![[Pasted image 20260208211541.png]]
---
### Connect on-prem to AWS #Video16
##### Site-to-site VPN (through internet)
- فكرة ال site-to-site VPN او ساعات بيسموه ال GW-to-GW VPN ان بوصل ال GW بتاعت ال on-prem هنقول انه ال edge router او ال Firewall ب Virtual private gateway اللي على ال VPC على AWS.
![[Pasted image 20260208212311.png]]

- لكن مشكلته ان اقصى سرعة نقل بينهم =  1.25 Gbps...عشان المشكلة دي ظهر النوع التاني.
##### Direct connect (physical connection)
- فكرته ان بيكون في حاجة اسمها AWS direct connect location بيكون فيه حاجة اسمها AWS cage فيه ال routers بتاعت AWS جمبه بنعمل Customer cage بيكون فيه ال routers بتاعت ال customer ونوصلهم ببعض directly وطبعا ده بيدي سرعات اعلى بكتير من ال site-to-site VPN توصل ل 100G وبيكون ==Dedicated connection==
![[Pasted image 20260208213102.png]]

- لو سأل في الامتحان عن حاجة توصل ال on-prem to AWS cloud بسرعة خلال ساعات هتكون ال site-to-site VPN..على عكس ال Direct connect اللي بتاخد اسابيع على ما تتعمل.
---
### VPC security #Video17
- اول الحاجة ال traffic بتيجي من ال internet على ال IGW وهنا الtraffic بتخبط في حاجة اسمها ==NACL== ودي بتكون على مستوي ال subnet كاملة وبتحدد هل ال traffic دي مسموحلها تعدي ولا لا 
- تاني حاجة بقا ال traffic بتخبط فيها هي ال ==security group== ودي بتكون على مستوى ال instances اللي جوا ال subnet.
##### NACL
- Allows or denies the inbound or outbound at the ==subnet level==.
- Each subnet must be associated with NACL (if not configured, there is a default one).
- The default NACL allows all inbound and outbound traffic.
- ==NACL is stateless==, the traffic should be allowed inbound and outbound.
##### Security group
- Allows or denies the inbound or outbound traffic at the resource level.
- ==By default the security group doesn't allow any inbound traffic, but allow all outbound==.
- ==Security group is stateful==.
---
### Route 53 #Video18
- Fully managed DNS service which resolve the domain name to IP.
- Route internet traffic to the nearest server
يعني لو ال IP اللي بيدور على موقع معين جاي من اوروبا ابعته على سيرفرات في ريجون اوروبا ولو جاي من امريكا ابعته على سيرفرات امريكا هكذا وده اسمه ==Global server load balancing = GSLB==
- Check the health of the server before replying with the IP of the server.
- Used to register my domain names.
- مهمة للامتحان ==Load balancing between regions==
---
### Block storage #video19 
##### EC2 instance store
- Used for ==temporary block-level storage==, and it is very fast.
- Ideal for caches, buffers, ==high IOPS ==and scratch data
##### EBS
- Can be used as primary storage device.
- Used for ==persistent block-level storage== like OS.
- ==It is suitable for running database.==
---
### Object storage #Video20
##### S3 (simple storage service)
- Stores data as objects in bucket.
- There are permissions for accessing the objects.
- Each object is file and its metadata.
- S3 durability 11x9's = 99.999999999%
- store infinite data.
---
### S3 storage classes #Video21
##### S3 standard
- Used to store ==frequently access data==.
- The data is stored in minimum ==3 AZs==.
##### S3 standard-IA
- Used to store ==less frequently access data==.
- Lower storage price than S3 standard, but ==extra retrieval fees==.
##### S3 one zone-IA
- Used to store less frequently access data in ==one zone==.
- Lower storage price than S3 standard-IA.
- Must be careful if you chose this type as the data in only 1 AZ, so if the AZ is down, you will lose the data.
- Use case: regeneratable data like sales reports and thumbnails. 
##### S3 intelligent tiering
- Monitoring the objects and move them to the suitable class based on object access numbers from the users.
- You pay small extra fees monthly per object for monitoring.
#####  S3 Glacier (==Archiving==)
1. ==Glacier instant retrieval==
	- Low cost for archiving data that is rarely accessed.
	- Retrieval time is milliseconds.
2.  ==Glacier flexible retrieval==
	- Lower cost than instant retrieval.
	- Retrieval time is minutes to hours.
3. ==Glacier deep archive==
	- Lowest cost.
	- Retrieval time within 12 hours.
---
### File storage #Video22
 - فكرة الFile storage بشكل عام ان في زي shared volume بين مجموعة hosts بيقدروا يوصلوا له من ال internet.
##### EFS (Elastic File System)
- Multiple EC2 instances in multiple AZs want to access same storage file system.
![[Pasted image 20260209115523.png]]

- ==EFS is compatible only with Linux== based EC2 instance, as it uses NFS protocol.
##### FSx
- ==FSx== is used with ==Windows== based EC2 instance, as it uses ==SMB or CIFS== protocols.
---
### Relational database #Video23 
- There is a relation between data.
- Data is stored in tables.
- SQL is used to store and query data.
- Fixed schema.
- تعالى نفهم ال on-prem ومشاكله ونشوف هنستفاد ايه..في ال on-prem كان الشركة بتجيب physical server تحط عليه الDB وتجيب DBA يبقى مسئول عن: 
					  1. السيرفر نفسه
					  2. الOS upgrading
					  3. الHA عشان لو السيرفر ده وقع
					  4. الstorage scalability عشان لو الداتا كترت والمساحة خلصت
					  5. ال backups
	طبعا دي مسئوليات كتير ووجع دماغ..الحل قالك بقا تعالى نروح ل AWS..بس عشان نروحلها في طريقتين
##### Self-managed DB
- دي زيها زي الon-prem باستثناء ان مبقاش عندي physical server.
##### Fully-managed DB (RDS)
- دي بقا Fully-managed by AWS انا مجرد بختار ال DB Engine اللي انا محتاجه وAWS بتحدد ال EC2 size وال OS وتعمله OS management وكمان لو محتاج مساحة (EBS) هي هتزود اوتوماتيك وكمان في option وانا بعملها اسمه multi-AZ عشان ال HA وبرضو في Auto backups.
- Fully-managed service that operates and scales on the cloud.
- Automate time consuming tasks of the DBA like managing servers, DB engine setup, OS patching and upgrading, HA, storage scalability and backups.
- Allow data encryption.
##### RDS engines
1. Aroura (MySQL or Postgres SQL) -> not opensource engines, compatible with Postgres SQL (3x better performance) and with MySQL (5x better performance) 
2. Postgres SQL
3. MySQL
4. MariaDB
5. Oracle DB -> expensive
6. Microsoft SQL -> expensive
##### Amazon Aurora
- ==Serverless==.
- compatible with MySQL and Postgres SQL
- Auto scale up to 128TB.
- Pay per second.
- 20% more expensive than RDS.
- ==Replicates 6 copies of data across 3 AZs==.
---
### Non-Relational DB #Video24
- Known as NoSQL or Key Value pair DB.
- Use different structure to store data rather than rows and columns.
- Dynamic schema.
##### DynamoDB
- ==Serverless== NoSQL DB service supports key value model.
- Designed to run high-performance applications.
- Scalable to handle millions of user requests.
- Can handle 10 trillion requests per day.
- Single digit millisecond performance even in high traffic.
---
### Migrate DB to AWS cloud #Video25
##### DMS (Database Migration Service)
- Used ==to/from== migrate on-prem or DB on another cloud provider to AWS cloud.
![[Pasted image 20260209134615.png]]

##### AWS Schema Conversion Tool (SCT)
- Used to converse the schema from DB engine to another one.
- لو مثلا عندي في ال on-prem بتاعي Oracle DB وعايز اعملها Migration على MySQL على AWS .. هنا ال Engines مختلفة فلازم احولها باستخدام SCT.
![[Pasted image 20260209135018.png]]
---
### Additional DB services #Video26 
##### Redshift
- Analyse Data across ==Datawarehouse==.
- ==OLAP = Online Analytical Processing==
##### Neptune
- ==Graph== database and social media analysis or recommendation.
##### DocumentDB
- ==MongoDB== compatible.
- NoSQL DB.
##### QLDB
- ==Centralized Ledger== DB.
##### Managed Blockchain
-   ==Distributed Ledger== DB.
##### Elasticache
- Caching layers to improve DB performance
##### DAX (DynamoDB Accelerator)
- Improve DynamoDB response time from milliseconds to microseconds.
---
### Shared responsibility model #Video27
- AWS is responsible about security ==of== the cloud (Physical infra).
- Customer is responsible about security ==in== the cloud (NACL and SG configuration).
- ==Patching OS is AWS responsibility if it is RDS (fully-managed), but it is customer responsibility if it is normal EC2 instance==.
---
### IAM #Video28 
- Service used to ==control== access AWS resources.
- عندي تيم داتابيز وتيم ستورج..عايز تيم الداتابيز ميكونش له access الا على الداتابيز وكذلك الستورج ميكونش له access الا على ال storage resources.
##### IAM user
- user for each person in the team for auditing.
##### IAM group
- group of IAM users with same responsibility.
##### IAM policy
- JSON document to allow or deny permissions on AWS resources.
- We attach the policy to IAM user or IAM group ==(Best practice to attach policy to a group instead of user)==.
- Always follow the ==least privilege security principle== (give permissions as required).
##### IAM role
- Used to give ==temporary permission== on resources.
##### MFA
- Additional layer of security (OTP).
##### Root user
- Created by default and the best practice to disable it, as it has the highest privileges and create another account to use it on daily tasks and only use the root user when needed.
---
### AWS organizations #Video29 
- AWS Organizations is an account management service that enables you to ==consolidate== multiple AWS accounts into an organization that you create and centrally manage.
- Organizations includes account management and ==consolidated billing== capabilities that enable you to better meet the budgetary, security, and compliance needs of your business.
- ![[Pasted image 20260224162336.png]]
##### Benefit of hierarchical grouping
- We have management account that manages member accounts, and we can group multiple member account is OU and apply policy on the OU.
- We can control the member account through ==Service Control Policy (SCP), and it is applied on OU==.
- ![[Pasted image 20260224162348.png]]
##### Benefit of billing consolidation
- ![[Pasted image 20260224163032.png]]
- لو التيرا بدولار في حالة انك اتسهلكت اقل من 10 تيرا ولو اكتر من 10 تيرا هتبقى التيرا ب 0.9 دولار .. وفي اكونت منفصل بيستهلك 3 والتاني بيستهلك 8 والتالت بيستهلك 6 ساعتها كل اكونت هيحاسب لوحده والتيرا هتبقى بدولار.. لكن لو جمعنا الاكونتات دي تحت organization واحدة ساعتها هيبقى كأن الفاتورة واحدة ب 17 تيرا وساعتها التيرا هتبقى ب 0.9 دولار.
---
### AWS WAF & Shield #Video30
##### AWS WAF
- AWS WAF lets you monitor the HTTP(S) requests (layer 7 protection) and protect against malicious traffic like ==SQL injection and XSS (cross site scripting)==.
##### AWS Shield
- Protects against Distributed Denial of Service ==(DDoS)== attack.
- AWS shield has 2 protection levels:
	1. ==Shield standard==: protection against ==layer 3 and layer 4 DDoS attacks== with ==no additional cost==.

	2. ==Shield advanced==: protection against ==layer 7 DDoS attacks== with real time reports and metrics, and support from ==Shield Response Team (SRT) from AWS==.
---
### AWS security services #Video31 
##### AWS Artifact
- AWS Artifact is a self-service portal that provides on-demand ==access to AWS compliance reports and security documentation==.
##### Amazon GuardDuty
- Amazon GuardDuty is a threat detection service that monitors and ==analyses the logs and events to identify the anomaly or malicious activities on AWS account==.
##### Amazon Inspector
Amazon Inspector is an automated ==software vulnerability== management service.
#####  Amazon Detective
- Amazon Detective analyses and visualize security data to get the ==root cause== of the incident.
---
### AWS security services #Video32 
##### AWS secret manager
- AWS secret manager helps you to manage, store, retrieve and rotate ==database credentials==.
##### Amazon Macie
- Amazon Macie is a fully managed data security service that uses machine learning to automatically discover, classify, and protect sensitive data such as ==PII in Amazon S3==.
##### Amazon Cognito
- Amazon Cognito lets you add ==sign-in and sign-up== to your web application and also social identity like sign-in with google account or Facebook.
---
### AWS security services #Video33 
###### Data encryption in transit
- ==Encrypt the traffic from the sender to the receiver==, like encrypting the credit card details while paying online.
###### Data encryption at rest
- Encrypt the stored ==data on storage devices==.
##### AWS certificate manager (ACM)
- ACM used to provision, manage and deploy public and private ==SSL/TLS certificates== for use
##### AWS key management service (KMS)
- KMS is used to provision, manage and control crypto keys that are used to ==encrypt the data at rest==.
---
### AWS monitoring and governance #Video34 
##### Amazon CloudWatch (Monitoring)
- Collects metrics and logs about the AWS resources.
- Monitor AWS and on-prem resources.
- Configure automatic alerts and actions.
- provide automatic dashboard.
##### Amazon CloudTrail (Auditing)
- Auditing service to audit all users, their actions, time of actions and how these actions were taken.
##### AWS trusted advisors
- Continuously monitoring the AWS resources and account and provide recommendations according to best practice across several categories like cost optimization, performance, security, fault tolerance and service limits.
---
### AWS pricing #Video35 
##### Free categories
1. Always free (first 1 million request for lambda every month).
2. 12-month free.
3. Free trial.
##### AWS cost explorer
- Tool to visualize and manage AWS cost.
##### AWS budget
- You set a budget (100$/month) for example, and this service will track the spending and alert you if you are near to become out of budget.
- It sends notification when:
	1. Your actual spending reaches 85%.
	2. Your actual spending reaches 100%.
	3. Your forecasted spend is expected to reach 100%.
##### AWS pricing calculator
- Used to estimate the cost of the AWS resources.
---
### AWS cloud migration strategies #Video36 
##### Retain
- keep it on-prem
##### Retire
- Close the application on-prem as it is not needed anymore.
##### Rehost
- Lift and shift, same infrastructure on-prem just moved to cloud.
##### Replatform
- Lift, tinker and shift, use some serverless services for example migrate DB on RDS.
##### Refactor (Re-architect)
- Huge change in architecture and it takes long time.
##### Repurchase
- Drop and shop.
- Drop the infrastructure and buy SaaS.
##### Relocate
- hyp, lift and shift.
- keep same hypervisor like VMware Esxi.
- VMware cloud (VMC) on AWS.
---
### AWS snow family #Video37 
##### AWS snowcone
- Small, rugged and secure physical device offering edge computing , data storage and data transfer on the go in little or no connectivity environment.
- Snowcone has 2 flavours:
	1. Snowcone -> 2 vCPUs,  4GB memory and 8 TB HDD.
	2. Snowcone SSD -> 2 vCPUs, 4GB memory and 14 TB SSD.
![[Pasted image 20260225221822.png]]
##### AWS snowball edge
- Snowball edge has 2 flavours:
	1. Storage optimized (80 TB HDD or 210TB NVMe).
	2. Compute optimized (28 TB NVMe or 42 TB HDD).
##### AWS Snowmobile
- Designed to move massive volumes of data up to 100 PB per unit
---
### AWS support plans #Video38 
##### Basic support
- 24/7 access to customer service.
- Free access to docs.
- Provide Personal health dashboard.
- ==limited access to trusted advisors==.
##### Developer support
- Email customer support and get reply within 24 hrs.
- Reply with 12 hrs with system cases like testing application.
- ==Great for experimenting, testing or POC==.
##### Business support
- ==Full access to trusted advisors==.
- 24/7 direct phone, web chat.
- Reply with 4 hrs with system cases and within 1 hrs when production is down.
- Minimum recommended plan if you have production.
##### Enterprise Ramp-on
- Response within 30 minutes for business critical workloads.
- Access for ==pool== of Technical Account Managers ==(TAM)==.
- Recommended with production and business critical workloads.
##### Enterprise support
- Response time within 15 minute.
- Access to ==designated TAM==.
- Recommended for mission critical workloads.
---
### AWS Well Architected Framework (WAF) #Video39 
- Helps cloud architects to build secure, high performing, resilient and efficient infrastructure for applications and workloads.
- Built around ==6 pillars==:
	1. Operational Excellence.
	2. Security.
	3. Reliability.
	4. Performance Efficiency. 
	5. Cost optimization.
	6. Sustainability.
##### Operational Excellence
- Run and monitor systems to deliver business value and improve supporting process and procedures.
- Perform operation as a code (Automation).
- Annotate documentation (Monitoring).
- Anticipate failure (Monitoring).
- Refine operations procedures.
- ==Make frequent, small and reversible changes.==
##### Security
- Ensure systems and data protection.
##### Reliability
- Ensure that the system can recover automatically, and also auto scale on demand.
##### Performance efficiency
- Using the resources efficiently to meet the system requirements.
- Go global in minutes.
- Experiment more often.
- Use serverless architectures.
##### Cost optimization
- Run the system with lowest cost and ensure it meets the business value.
##### Sustainability
- Minimize the environmental impacts of running cloud workloads.
---
### AWS Cloud Adoption Framework (CAF) #Video40 
- Helps the organizations migrate to cloud using their experience and best practice.
- ![[Pasted image 20260226222945.png]]
### AWS Marketplace
- Digital catalog for customers to find and buy ==third party software== to run their business.
---
### AWS AI and Machine learning #Video41 
##### AWS Rekognition
- Amazon Rekognition is an Amazon Web Services (AWS) offering that makes it easy to add ==image and video analysis== to applications using deep learning technology, requiring no machine learning expertise.
- Object detection.
- Facial detection.
- Text detection.
- Unsafe detection.
##### Amazon Polly
- Amazon Polly is a ==text-to-speech== service.
##### Amazon Transcribe
- Amazon transcribe is a ==speech-to-text== service.
---
### AWS AI and Machine learning #Video42
##### Amazon Translate
- ==Translation== service.
##### Amazon Lex
- Used to build ==interactive chatbot== like siri and alexa.
##### Amazon comprehend
- Amazon comprehend uses ==NLP== to extract insights from data.
- Sentiment analysis.
- Document classification.
- Language detection.
- Entity recognition.
---
### AWS AI and Machine learning #Video43
##### Amazon SageMaker
- Amazon SageMaker is fully managed service to ==build, train and deploy machine learning models==.
##### Amazon Kendra
- Document search service.
##### Amazon Textract
- service that is used to extract the handwriting text from documents.
---
### Notes from solving
- *AWS step functions* -> fully managed, serverless ==orchestration service== that enables you to build resilient, visual workflows known as state machines to coordinate AWS services, microservices, and SaaS applications, ==loosely coupled architecture==.
- *Amazon Personalize* -> Enhance your digital transformation with ML, seamlessly integrating personalized ==recommendations== into websites, applications, email system.
- *AWS Service Catalog* allows IT administrators to ==create, manage==, and distribute curated, pre-approved catalogs of IT services (e.g., virtual machines, databases, application architectures) for end-users to deploy in a ==self-service manner==.
- *AWS Systems Manager Parameter Store* provides secure, hierarchical, and ==centralized storage for configuration data and secrets==, such as database strings, passwords, and API keys.
- *AWS CLI* -> unified tool to provide a consistent ==method to interact with AWS services==.
- Applying updates to the *Nitro Hypervisor* is an AWS responsibility. The Nitro Hypervisor is a component of the underlying infrastructure ==managed by AWS==.
- *IAM Access Analyzer* -> Find out which ==resources are shared externally== like S3 Buckets, IAM Roles, KMS Keys, Lambda Functions, and Layers SQS queues and Secrets Manager Secrets
- *AWS Application Discovery Service* -> ==helps enterprises plan migration== projects by automatically identifying on-premises servers, virtual machines, applications, and their dependencies. It ==captures configuration data, performance metrics==.

> Transferring data to the cloud is free, and also transferring the data between resources in same region or same availability zone.

- *AWS Firewall Manager* is ==a security management service that centralizes the configuration and deployment of firewall rules across all accounts and resources in== AWS Organizations. It simplifies managing AWS WAF, Shield Advanced, VPC Security Groups, Network Firewall, and Route 53 DNS Firewalls.
- *Spot Instances* are recommended for stateless, fault-tolerant, flexible applications.
- *AWS DataSync* migrate data from on prem to cloud with validation and encryption.
- *Amazon Connect* service to connect customer service and it is AI-powered. 