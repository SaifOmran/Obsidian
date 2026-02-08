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
