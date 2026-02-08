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
- Suitable for short term, spike and unpredictable workloads
- Can NOT be interrupted by AWS.
##### Spot instance
-  The user use the unused EC2 capacity with 90% discount
- It can be interrupted by AWS at any time. 
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
- نرجع خطوتين لورا للself-managed EC2 instance اللي كان لازم اظبط ال OS واعمل sizing لل resources بتاعتها..كل ده معناه اني بعمل provisioning .. في ال serverless انا مش بعمل provisioning .. انا بس برفع الكود بتاعي وAWS مسئولة تحدد ال resources اللي الكود محتاجها وبتحاسب بال millisecond لما بيحصل trigger للكود عكس ما كنت بتحاسب بالثانية على ال EC2 instance مادام هي up and running حتى لو بت server شخص واحد.
- ==Run code without provisioning and managing infrastructure.==
- Saves costs as you pay for compute time by ==millisecond==.
- AWS offers up to 1 million free request / month.
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
