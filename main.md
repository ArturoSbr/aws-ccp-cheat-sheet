# About this cheat sheet
This markdown document contains the main concepts that you'll be tested on when taking the AWS Certified Cloud Practitioner (CCP) exam.

## Identity Access Management (IAM)
IAM is a global service that allows us to manage users, groups, policies and roles. We can assign permissions to users or groups of users using Policies. Roles are policies that can be attached to entities. They are necessary for services that run actions on our behalf (like EC2).

Within IAM, we can generate a Credential Report that lists the status of the credentials owned by the users in the account. We can also view the allowed services of any user with Access Advisor.

### Root User Privileges
The root user is the account owner and has access to all of AWS. The root user has more permissions than even the highest-ranking admin. Some of the tasks that can only be performed by the root user are:
1. Change account settings (name, email, root user password and root user access keys).
2. Close account.
3. Change AWS Support plan.
4. Register in the AWS Marketplace.

## Elastic Compute Cloud (EC2)
EC2 is a services that allows us to rent virtual machines. We can choose the operating system, compute power (CPU), RAM, storage and network card. We can configure an instance's firewall rules, its bootstrap script, etc.

### EC2 Purchasing Options
There are four options to rent EC2 instances.
1. On-Demand: Most expensive, billed by seconds used, minimum time billed is 60 seconds.
2. Reserved Instances: Commit to renting an instance for one or three years. Instance type, region, Availability Zone (AZ) and OS are fixed for this period. We get a discounted rate whenever we start an instance that matches the attributes of an active reserved instance.
3. Savings Plan: Commit to an hourly spend commitment between one or three years (i.e., $0.15/hour for one year). Instance family and region remain fixed.
4. Spot Instances: Define a maximum price you are willing to pay and compare that against the market's spot price. If the spot price is greater than our threshold, we lose the instance.
5. Dedicated Hosts: Reserve a physical server from AWS.

### EC2 Storage Options

#### Elastic Block Store (EBS)
We can attach EBS volumes to an EC2 instance for storage. EBS volumes are network drives (they are not physically attached to an instance) and are locked to an availability zone. To move a volume between AZs, we need to take a snapshot of the original volume and then create a new volume from the snapshot in the desired AZ. To move a volume between regions, we need to snapshot a volume, copy the snapshot to another region and then create a new volume from the snapshot. EBS volumes are useful because they allow us to persist our data. So if an instance fails, we can start where we left off by attaching the old instance's drive to the new one.

At the CCP level, EBS volumes can only be attached to a single EC2 instance.

#### Instance Store
Since EBS volumes are not physically attached to EC2 instances, their performance is not as good as a physical drives. An Instance Store is a high-performance hardware disk that has better IOPS than an EBS drive. However, the data is lost if the EC2 is stopped or terminated.

#### Elastic File System (EFS)
EFS is a network file system that can be mounted on multiple Linux EC2 instances at the same time. It is an improvement over EBS because it let's us share storage across multiple EC2 instances over multiple AZs (i.e., two EC2 instances can write on the same storage system and the data is synced).

#### FSx
Used for launching third-party file systems on other operating systems (Windows and Lustre).

### Amazon Machine Image (AMI)
AMIs are customizations of EC2 instances that specify the OS, configuration and software installed. AMIs are region-locked, so we must create a copy if we want to move an AMI across regions. AMIs are useful to automate the standardized creation of new instances. We can get faster boot times by starting instances with AMIs because all the software is pre-packaged.

To create an AMI, we can customize an EC2 instance, stop it and create an AMI from it. We can then launch EC2 instances from this AMI.

### EC2 Image Builder
It is a service used automate the process of creating, maintaining and testing EC2 AMIs.

### Security Groups
We can define Security Groups to create a firewall around our EC2 instances. This way, we can control the traffic that comes in or out of our instances. The most common protocols to communicate with an EC2 instance are HTTP, HTTPS and SSH. We can use EC2 Instance Connect to connect to our instance through our browser without explicitly setting up SSH.

We can assign IAM Roles to our EC2 instances to be able to use AWS services from them.

### Application Load Balancer (ALB)
An ALB is a single point of access that redirects traffic to multiple EC2 instances to spread loads, avoid congestion and handle failures of downstream instances. An ALB needs a Security Group to allow incoming requests as well as a Target Group (which groups EC2 instances together and redirects traffic to them).

In addition to ALBs, there are Network Load Balancers (NLBs) which are used to handle millions of requests per second, as well as Gateway Load Balancers (GLBs), which redirect traffic to a firewall and then back to the balancer.

### Auto-Scaling Group (ASG)
An ASG is a group of EC2 instances that belong to the same Target Group of a Load Balancer. ASGs can add or remove instances based on current traffic or health status (i.e., terminate unhealthy instance and replace it with a new one.). We can specify the minimum, maximum and desired number of instances. The ASG will automatically register new instances in the Target Group so that they are registered under the Load Balancer. ASGs need a Launch Template to determine how to launch new instances when traffic increases.

There are five main Auto-Scaling strategies.
1. Manual: Manually add or remove instances.
2. Simple/Step: Add or remove instances based on rules (i.e., if CPU usage exceeds 90%, add one instance).
3. Target: Set a target metric and scale instances to meet that demand (i.e., keep average CPU usage at 70%).
4. Scheduled: Adjust capacity at certain pre-scheduled times.
5. Predictive: Learn from historic usage and scale based on the latest forecast.

## Simple Storage Service (S3)
S3 is a service where we store objects (files) into buckets (main directories). Buckets must have a globally-unique name across all of AWS and can contain folders within them. Buckets are created in a region and are available everywhere.

Objects are organized by keys. A key is composed of a prefix and an object name. For example, the key `s3://my-s3-bucket/my-file.jpeg` is composed of:
1. The prefix `s3://my-s3-bucket/`; and
2. The object name `my-file.jpeg`.

Objects can have metadata, which are key-value pairs defined by the system or the user. We can also add tags to objects for organization purposes. Finally, S3 is supports versioning (if enabled, in which case, objects will also have a version ID.).

Users can access S3 resources in two ways:
1. Explicitly assign permissions (or roles) to users or groups of users through IAM; or
2. Assign a Bucket Policy to the bucket in question.

### Replication
We can set up Replication Rules to make sure that the content in an S3 bucket is replicated in another bucket (even if the other bucket belongs to a different AWS account). To do so, we must enable versioning on both buckets and then set up a Replication Rule. This is useful if you want to sync two buckets (to reduce latency, for example.).

### Storage Classes
There are multiple storage classes which vary in pricing and retrieval time.

#### S3 Standard
Storage class used for data that is frequently accessed and needs to be available with low latency. The data is stored in multiple AZs, so it can endure AZ failures. Retrieving data is free.

#### S3 Infrequent Access (S3 IA)
Storage class used for data that is not accessed frequently but still needs fast access when needed. It is less expensive than S3 Standard and is also available in multiple AZs. There is a cost per GB retrieved and the minimum storage duration is 30 days.

S3 One-Zone Infrequent Access is the same as S3 IA except that it is only available in a single AZ. The data is lost if the AZ is destroyed.

#### S3 Glacier
S3 Glacier is a storage class meant for archiving data. There is a cost for storage size as well as for retrieving data.

1. S3 Glacier Instant Retrieval: For data that is accessed every quarter. Retrieve data in milliseconds. Minimum storage duration is 90 days.
2. S3 Glacier Flexible Retrieval: For data that is accessed once or twice a year. Retrieval time varies between one minute and 12 hours. Minimum storage duration is 90 days.
    a. Expedited: One to five minutes.
    b. Standard: Three to five hours.
    c. Buld: Five to twelve hours (no retrieval cost).
3. S3 Glacier Deep Archive: For data that is accessed less than once a year. Retrieval time varies between 12 and 48 hours. Minimum storage duratino is 180 days.

### S3 Intelligent-Tiering
A service that monitors how often you access your data and moves objects from one tier to another to minimize your costs. There are no retrieval charges in S3 Intelligent-Tiering, but there is a small fee for monitoring the usage of your data.

## Databases, Analytics and AI
This section covers some of the AWS services dedicated to databases, analytics and Machine Learning (ML).

### Amazon RDS
RDS is a managed relationship database service compatible with MySQL, PostgreSQL, Oracle SQL, MariaDB and more. It is made for Online Transaction Processing (OLTP). The advantage of using RDS (or any managed database service) is that Amazon is responsible for the security of the  server hosting the database, its OS, etc. This way, we can focus on just deploying the database and connecting it to our application. Moreover, we can set up failover strategies more easily (such as setting up a failover database in another AZ). RDS is included in the free tier.

Read Replicas are copies of your database that your application can read from (only read; writing remains centralized). This way, the queries sent by your application will be distributed across multiple instances (with the same data).

### Amazon ElastiCache
ElastiCache is a managed in-memory database service that comes in handy whenever an application performs many reads. Instead of running the same query many times, the result is cached and made readily available for other calls that are querying the same data.

### Amazon Aurora
Aurora is similar to RDS except it only works with PostgreSQL and MySQL. It is slightly more expensive than RDS but offers better performance. Additionally, Aurora increases its storage automatically, so we do not have to provision additional storage. Aurora is not included in the free tier.

### DynamoDB
DynamoDB is a serverless solution (i.e., we do not need to instantiate a database) for low-latency NoSQL databases (i.e., databases that store unstructured data that is not relational). You skip straight into creating a table without the need to instantiate a database.

### DynamoDB Accelerator (DAX)
A fully managed service that caches common requests (like ElastiCache but especially made to integrate with DynamoDB).

DynamoDB tables can be turned into Global Tables to allow synced read/writes in multiple regions. This is called active-active replication because we can actively read/write in one region and it will be actively replicated in another region.

Active/Passive meaning:
- Active: allows read/writes.
- Passive: Only allows reads.

### DocumentDB
In the same way that Aurora is a proprietary version of PostgreSQL/MySQL, DocumentDB is a proprietary version of mongoDB which is used to store JSON data. DocumentDB is like Aurora for No-SQL.

### Redshift
Redshift is a managed database service for Online Analytical Processing (OLAP) instead of OLTP. The data is written periodically; not continuously. It is used to host data warehouses for analytical processing. It can be integrated with BI tools such as AWS Quicksight or Tableau to create dashboards.

### Elastic Map Reduce (EMR)
EMR is used to create Hadoop, Spark, Presto and other types of clusters. These clusters can be composed of many EC2 instances and they all process the data together. EMR is a service that provisions and configures all the instances to create a cluster.

### Athena
Athena is a serverless solution that allows us to use SQL to run queries on S3 objects. The objects can be stored in CSV, parquet, JSON and other file formats (i.e., they do not have to be stored in a table!). Athena queries are billed depending on the TBs of data scanned.

### Neptune
Amazon Neptune is a managed graph database service. It is useful for storing highly-connected datasets, such as social networks.

### Quantum Ledger Database (QLDB)
QLDB is a serverless solution used to store financial transactions using SQL. It is a centralized ledger that guarantees that once something is written to the database, it cannot be deleted or modified. It is centralized to comply with regulation.

### Managed Blockchain
Managed Blockchain is a serverless solution that allows parties to execute transactions without a centralized ledger. It is compatible with Hyperledger and Ethereum.

### Database Migration Service (DMS)
DMS is a service to safely migrate one database to another. The source database can remain active during the migration. DMS allows us to migrate from one engine to another (for example, PostgreSQL to Oracle SQL).

### Glue
Glue is a serverless ETL solution. It is the middle component between our raw data (stored in S3, RDS, etc.) and a data warehouse (Redshift). Glue allows us to run a script that transforms the raw data and loads it into the warehouse.

### Quicksight
Quicksight is a service to create dashboards. It can connect to RDS, Aurora, Athena, Redshift, S3, etc.

### SageMaker
SageMaker is a manages service where we can do the whole process of developing and deploying a model. In SageMaker we can gather data, label it, train a model and deploy it. We do not have to worry about instantiating servers to train or receive calls because they are all managed by SageMaker.

### Pre-trained AI Services
1. Rekognition: Face detection, labeling, celebrity recognition.
2. Transcribe: Audio to text.
3. Polly: Text to audio.
4. Translate: Translate to another language.
5. Lex: Understand chats/calls to build a bot that users can talk to.
6. Connect: Create contact flows to set up a virtual contact center (hand in hand with Lex).
7. Comprehend: Get insights from natural language (sentiment, purpose of a sentence, etc.).
8. Kendra: We input documents with information and the user can ask questions to Kendra (i.e., where is the bathroom?).
9. Personalize: Real-time recommendation system for our applications.
10. Textract: Extract text from scanned documents.
11. Forecast: Predict future values of time series.

## Other Compute Services

### Elastic Container Service (ECS)
Used to launch Docker containers on EC2 instances. We must launch the EC2 instances in advance, and ECS will choose which instance to host the container on.

### Fargate
Fargate is a serverless service to launch Docker containers. In contrast to ECS, we do not need to worry about provisioning the infrastructure.

### Elastic Container Registry (ECR)
ECR is where we store our Docker images so that we can launch them on Fargate.

### Lambda
Lambda is a fully serverless solution to run functions on-demand. In contrast to EC2, we do not need to have an instance running continuously to execute a function every time we need it. Instead, Lambda functions are hosted by AWS (we do not need to worry about the infrastructure) and they are not running continuously; they are event-driven (i.e., they only activate when an AWS trigger is detonated.). Scaling is fully automated (no need to define a load balancer or auto scaling group). We are billed for each call we make and the time it takes to run the function.

### API Gateway
API Gateway is a managed service to expose the API of a Lambda function so that the end user can invoke the function. It allows us to create, publish and maintain APIs in the cloud.

### Batch
Batch is a managed service to run batch jobs packaged in Docker containers. Batch provisions optimized EC2 instances to run our batch jobs. The service runs on ECS and it auto-scales automatically.

### Lightsail
Lightsail is a beginner-friendly alternative to EC2 instances. The services provides us with virtual servers, storage, databases and networking to deploy simple web-applications (because it does not auto-scale).

## Infrastructure

### CloudFormation
CloudFormation is referred to as Infrastructure as Code. The service allows us to deploy infrastructure by passing it a YAML file with all the resources we need. CloudFormation will figure out the order in which the services need to be launched and will connect them together. Another advantage is that the process is not manual, so can use the same code to deploy the same infrastructure in different environments. Finally, all the resources from the same CloudFormation stack share the same tags, so it makes tracking costs easier.

### Cloud Development Kit (CDK)
CDK is the same as CloudFormation except that we can configure our environment using other languages (Python, Typescript, etc.) instead of YAML. CDK will parse the code and translate it to a usable YAML template.

### Elastic Beanstalk
Beanstalk is a Platform as a Service (PaaS) that allows us to deploy an app in a single developer-friendly platform. Beanstalk handles instance configuration, the deployment strategy, capacity provisioning, load balancing, auto-scaling and monitoring (there is a monitoring suite inside BeanStalk!). The difference between CloudFormation and Beanstalk is that CF can be used to deploy any kind of infrastructure, whereas BS is more focused on deploying applications (not just the infrastructure) in a centralized platform.

### Deploying Code
1. CodeDeploy: Automate software deployments to a hybrid mix of servers (EC2 or on-premises). Each instance must have an agent installed.
2. CodeCommit: AWS's proprietary version of GitHub.
3. CodeBuild: Service that allows us to compile, test and package our code in the cloud. The final package is executable by CodeDeploy.
4. CodePipeline: Allows us to automate the steps for pushing code to production (for example, code, build, test, provision and deploy.).
5. CodeArtifact: Artifact management system which acts as a place where we can store and retrieve code dependencies.
6. CodeStar: PaaS that unifies all the previous services (unified UI )
7. Cloud9: Cloud IDE that can be accessed from your web browser to open the same project from multiple clients to collaborate.
8. CodeGuru: ML-powered service that can do automated code reviews (CodeGuru Reviewer) and performance recommendations (CodeGuru Profiler).

### Managing Fleets of Servers
#### Systems Manager
Hybrid (works with cloud and on-premises infrastructure) user interface to manage our EC2 systems (view, control and patch). We can automate patches, run commands on the entire fleet, etc. An agent needs to be installed on every instance.

#### SSM Session Manager
Start a secure shell on EC2 an on-premises servers through SSM. It is safer because we do not have to enable SSH access or create SSH keys. 

#### OpsWorks
Server configuration with Chef and Puppet (like SSM but for these technologies).

##  Global Infrastructure
The idea of deploying a global application is to host it in multiple regions to reduce latency for users around the world as well as to increase failover to other regions in case of attacks or disasters.

### Route 53
Route 53 is a managed DNS service. It allows us to create records that map domain names to IP addresses. For example, clients can request data from `http://MyDomain.com` and Route 53 will reply to them with the IP address where our app is hosted so that they can request data directly from the host's IP.

Route 53 allows us to configure Routing Policies.
1. Simple RP: No health checks. Client accesses domain and Route 53 responds with the host's IP.
2. Weighted RP: Distribute traffic to multiple EC2 instances. Route 53 redirects traffic according to the weights we configure (for example, 50% traffic to instance 1, 30% to instance 2 and 20% to instance 3.).
3. Latency RP: Redirect traffic to the closes host. Route 53 checks the location of the client and responds with the IP of the host closest to them.
4. Failover RP: Route 53 can do health checks on a primary EC2 instance and will redirect all traffic to a failover instance if the primary fails.

### CloudFront
CloudFront is a Content Delivery Network (CDN) that allows us to replicate part of our app in multiple edge locations or to cache our app's content at edge locations. This greatly improves read performance. It uses AWS Shield as well as AWS Web Application Firewall (WAF) to protect our app against DDoS attacks (check Security section). CloudFront connects to S3 buckets and HTTP servers (called "origins"). It stores requests in local caches and delivers the cached content to users close by. The origins can be protected using Origin Access Control and S3 Bucket Policies.

### S3 Transfer Accelerator
Speed up client &rarr; edge location &rarr; S3 Bucket. S3 Transfer Accelerator allows us to read/write to an edge location through the public web. The data is then sent from the edge location to the S3 bucket through the AWS private network. It is different to CloudFront because reads are not cached and allows writes. Transfer Accelerator connects the client directly to the S3 bucket. The service also integrates with Shield for DDoS protection.

### Global Accelerator
Global Accelerator allows clients to connect to edge locations and routes the traffic through the AWS private network. It is different to S3 Transfer Accelerator because it works with other services apart from S3. For example, we can set up an ALB in Germany and users can interact with our app through an edge location which routes the traffic directly to the ALB through the private network. Once again, the content is not cached. The service also integrates with Shield for DDoS protection.

### Outposts
Outposts are AWS server racks that AWS installs in your company's premises so that you can use AWS as if you were in the cloud, but the services are running locally (from the racks installed). For example, if you start an EC2 instance in an outpost, it will behave the same way as before, but it will be hosted from within the company's premises. The users are now responsible for the physical security of the racks, but it gives us low-latency and our data never leaves our premises.

### WaveLength
Deploy infrastructure on nodes from the 5G network. This is like deploying infrastructure on edge locations, except the nodes are owned by a 5G telecom carrier instead of AWS. For example, we can start an EC2 instance in a wavelength zone, which means that the server will be hosted in the carrier's data center in that node. It is used for ultra low-latency applications, such as gaming.

### Local Zones
Local Zones allow us to create subnets so that we can add an AZ from one region to another region. It is useful to create "custom regions".

## Cloud Integrations and Monitoring
This section covers services that can be used to integrate two apps together so they can communicate. It also covers the monitoring of applications (logs, events, etc.).

### Simple Queuing Service (SQS)
There are two ways that two apps can communicate with one another. The first is to have them post and receive requests synchronously, which means that both ends are always listening. This can be problematic when demand spikes (because our processing infrastructure has to scale very quickly). The second way is called asynchronous communication, which consists of putting an intermediary between both applications to regulate the flow of traffic.

SQS is a serverless service that allows us to decouple our applications by putting a queue between both applications. This way, when the producers send many requests, they are stored in a queue and the consumers can poll the messages to start processing them one by one. Wait time will be long, but it will not overwhelm our backend. The queue does not have a limit and the queued messages can stay there for up to 14 days. Messages are deleted after they are read by consumers, and they can share the workload by scaling horizontally.

For example, the producers can receive many requests from clients and scale with an ASG. The messages are sent to a queue, which routes them onto the consumer instances. The benefit of doing this is that consumers can have an independent ASG, which is why querying messages is referred to as decoupling our applications.

### Simple Notification Service (SNS)
SNS is used to send one message to multiple services (one to many). It is different from SQS because the relationship is one to one in SQS.

SNS decouples applications by sending a message to an SNS Topic. These messages are sent by SNS "publishers", and the receivers are called "subscribers", which get all the messages sent to a topic. Examples of SNS subscribers are Lambda functions, Kinesis Streams, an SQS, HTTP endpoints, etc.

### MQ
SQS and SNS are proprietary message brokers. MQ is a managed service to decouple applications using open protocols, such as MQTT, AMQP, etc. It is compatible with two pub-sub services: RabbitMQ and ActiveMQ. It is useful in case we have physical infrastructure and are migrating to the cloud (we can use MQ and keep using our streams instead of re-engineering everything to make it compatible with SQS or SNS.).

### Kinesis
Kinesis is a serverless service used to ingest data with low-latency from many sources. Kinesis Firehose is a complimentary service to load these data points to S3, Redshift, etc. Kinesis Data Analytics can be used to perform real-time analytics on data streams using SQL. Kinesis Video Streams is used to monitor video streams in real-time.

### CloudWatch
CloudWatch is a monitoring service used to track metrics, logs and trigger alerts and actions.

#### Metrics
CloudWatch Metrics is used to monitor metrics from any service on AWS. For example, we can monitor the CPU utilization of our EC2 instances over time. We can visualize the evolution of any metric over time in a CloudWatch Metrics dashboard.

#### Alarms
CloudWatch Alarms are notifications that get triggered when a CloudWatch Metric passes a pre-established threshold. Alarms can trigger actions or send notifications. For example, we can send an SNS notification, stop an EC2 instance, increase the number of instances in an ASG, recover an instance if it fails, etc.

To create an alarm, we can set the threshold (min, max, average, sum, etc.) and the time period during which the metric will be evaluated. Billing Alarms are only available in us-east-1.

#### Logs
CloudWatch logs allow us to monitor logs in real-time. CloudWatch logs can be collected from Elastic Beanstalk, ECS, Lambda functions, CloudTrail, Route 53 and CloudWatch log agents installed on EC2 instances. By default, EC2 instances do not create logs, so we need to install a CloudWatch log agent and select the logs we want to push into CloudWatch. To do this, we need to attach an IAM Role to our instance to allow it to write to CloudWatch.

### EventBridge
EventBridge enables us to create rules to schedule cron jobs (run services periodically) or run jobs based on a service's usage pattern (i.e., set up a rule that sends an SNS to a topic whenever the root user logs in.). It is different to SNS because ???

### CloudTrail
CloudTrail provides you with a history of all the actions and API calls made within our AWS account. It is useful to find the answer to who, what and when questions.

If we want to retain these logs for long periods of time, we can send CloudTrail actions to CloudWatch Logs or an S3 bucket.

### X-Ray
It is hard to trace bugs when we have an application that is made up of many decoupled applications. X-Ray allows us to visualize the actions in our application and makes it easy to trace bugs.

### Health Dashboard

1. Service health: Shows the daily health of all the services in every region.
2. Your account health: Provides personalized information regarding issues of services that affect your applications.

## Virtual Private Cloud (VPC)
VPCs are partitions of the AWS cloud where we can deploy our infrastructure. The following diagram shows the basic structure of the default VPC.

![Default VPC structure](https://github.com/ArturoSbr/aws-ccp-cheat-sheet/blob/main/figures/vpc.png)

- Virtual Private Cloud: Tied to an AWS region.
- Subnets: Tied to an AZ.
- Route Tables: Routes used to connect subnets together or connect to the internet.
- NACL: Subnet-level firewall with a list of allowed and denied IPs.
- Security Groups: Instance-level firewall with a list of allowed IPs or other SGs.

### VPC Flow Logs
Logs to monitor IP traffic coming in our out of our VPC (allowed or denied).

### Connections
1. VPC Endpoints: Allow us to connect to AWS resources through a private network (more secure, reduced latency).
2. VPC Peering: Connect a few VPCs together using the AWS private network to make them behave a single network. The connections are not transitive! For example, if A is peered with B and B is peered with C, we need to create a connection between A and C manually.
3. VPC PrivateLink: Connect many VPCs together (like VPC Peering, but for a larger network).
4. Site to Site VPN: Encrypted connection from your on-premises data center to your AWS VPC though the public internet.
5. Direct Connect: Create a physical connection to AWS.
6. Client VPN: Connect your computer to your VPC so that you can connect to EC2 instances through their private IPs (instead of using their public IPs, which is less secure).
7. Transit Gateway: Service to connect many VPCs and on-premises data centers through a "hub and spokes" topology (TGW in the center).

## Security

### Penetration Testing
We can attack our own infrastructure without asking for permission from AWS for the following services: EC2, ELB, Lamda functions, RDS, Aurora, CloudFront, NAT gateways, API gateways, Lightsail resources and Beanstalk environments. We cannot attack our infrastructure by flooding (DDoS, port flooding, etc.).

### DDoS Protection
1. Shield Standard: Free DDoS protection for your websites and applications.
2. Shield Advanced: Premium DDoS with 24/7 surveillance.
3. Web Application Firewall (WAF): Filter requests based on rules (e.g., only one request per 10 minutes).

### Encryption
#### Key Management Service (KMS)
We use KMS to encrypt data and objects. AWS manages the keys and our job is to define who can access them to decrypt encrypted services. For example, we can encrypt an RDS database and select users that can access the keys to decrypt it.

#### Cloud Hardware Security Modules (HSM)
AWS provisions hardware for encrypting data and our job is to use it to create and manage encryption keys.

#### Types of Customer Master Keys (CMK)
1. Customer Managed CMK: Keys that are created and managed by the user.
2. CloudHSM Keys: Keys created with an HMS device through CloudHSM.
3. AWS Managed CMK: Keys that are created and managed by AWS on our behalf.
4. AWS Owned CMK: Keys owned by AWS that we cannot even see.

### Amazon Certificate Manager (ACM)
Service to deploy SSL/TLS certificates. It can be attached to an ALB so that the user can communicate with it via HTTPS.

### Secrets Manager
Service to store secret access keys for RDS, Redshift, S3, etc. The secrets can be retrieved from applications (i.e., a python script) or the AWS CLI (that way we don't have to hardcode our keys into the code).

### Artifact
Portal where we can access the compliance documentation and agreements. For example, we can see the compliance reports made by third-party auditors, such as ISO certifications, PCI reports, etc. These reports are usually accessed to show that your company can adopt AWS without violating any regulations.

### Macie
Macie is a fully managed service that scans your data to detect sensitive information. Macie notifies you via EventBridge when it discovers data that can be classified as sensitive.

### GuardDuty
Automated service that uses ML to discover anomalies in your account. The service looks at your logs (CloudTrail, S3, etc.) to determine if something unusual is going on. It can be synced with EventBridge to send SNS notifications when the service finds something suspicious.

### Inspector
Run automated security assessments for EC2, ECR and Lambda functions. It will report its findings to the AWS Security Hub as well as EventBridge.

### Detective
This service tracks the source of a security issue found by GuardDuty, Macie and SecurityHub.

### Config
Config stores changes in the configuration of your AWS account and services over time (like source control but for configuration settings). It can trigger SNS notifications to alert us of new changes. The service has to be enabled and is not free.

### SecurityHub
SecurityHub is a centralized tool to automate security checks and manage the security of multiple AWS accounts. It has a security dashboard that aggregates alerts from all the previous services. SecurityHub requires Config.

### Abuse
Abuse is a portal where we can report AWS resources that are being used for abusive or illegal purposes (spam, port scanning, DDoS attacks, hosting copyrighted content, etc.).

## Account Management and Billing
### Organizations
Organizations is a global service that allows us to link multiple AWS accounts into a single organization to get consolidated billing, volume discounts, share reserved resources and control permissions using Service Control Policies (SCP). It also allows us to automate the creation of AWS accounts (useful when a company sets up one account per department or employee).

### Control Tower
Control Tower runs on top of Organizations and it is a service to govern a multi-account AWS environment based on best practices. It allows us to automate the setup of AWS environments, detect policy violations and monitor account compliance using a dashboard. This is done by setting up a Landing Zone, where we add one Master Account, a Logging Account and an Audit Account. AWS recommends to manage Organization Units (OUs) via Control Tower and not within Organizations itself!

### Pricing Models
1. Pay as you go: Pay for what you use as you deploy new infrastructure.
2. Reserve resources: More predictable budgets by reserving resources in advance for a predetermined amount of time.
3. Volume discounts: Get better rates for using high volumes (such as in S3 or consolidated billing).
4. AWS economies of scale: As AWS grows, they can offer better prices for us.

### Free tier
1. IAM
2. VPC
3. Consolidated billing
4. EC2 t2.micro, S3, EBS, ELB, AWS Data Transfer, RDS
5. CloudFormation, Elastic Beanstalk
6. Autoscaling groups

