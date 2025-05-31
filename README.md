# AWS DevOps Essentials
## If I'll find any other feature to be useful - it'll surely be added!
## Basics
AWS is a cloud platform that offers many networking, computing, storage, etc. services. It allows for building scalable, flexible and automated IT infrastructure.
### Regions & Availability Zones
#### Regions
Regions are physical locations in the world where AWS hosts their data centers. Each region consists of separate and isolated Availability Zones. Regions are independent of each other and may differ e.g. in laws \(e.g. RODO in Europe\). When creating infrastructure remember to choose regions close to end users of infrastructure, it minimizes latencies.\
Some regions examples:
* us-east-1 - Northern Virginia, USA
* eu-west-1 - Ireland
* ap-southeast-1 - Singapore
#### Availability Zones
Availability Zones are one or more physical data centers located in region. Their infrastructure is isolated from each other, but they are connected by low-latency, private networks. Thanks to this availability zones are not affected by each others failures. You can replicate data and balance network traffic to different availability zones in region to ensure high availability and efficiency.
### Identity and Access Management \(IAM\)
IAM manages access to resources and services in cloud. Access is based on created users and their assigned groups, roles and permissions. Thanks to this you can strictly control who and what can do in your cloud.

**Users** are individual persons or applications that can access and use cloud. \
**Groups** are sets of users that can be assigned common permissions.\
**Roles** are identities with specific permissions and no password or key, that can be temporarily assumed by entities (e.g. services).\
**Policies** are documents that define permissions and resources they affect. Attach them to users, groups and roles \(best practise - attach to groups or roles\).

Always follow the **Principle of Least Privilege** that states that users should only have the permissions that they need to perform their tasks.\
Use **Multi-Factor Authentication \(MFA\)** to enhance security of users accounts.\
Avoid using **root account**, use it only to create new users.\
**Automate** IAM with IaC!
### Billing & Costs
Managing costs is a must - always do it carefully to avoid unexpected costs and optimizing them without compromising performance.

AWS uses **Pay-As-You-Go payment model** which means that you only pay for what you use, e.g. if you use EC2 instance for 5 hours - you pay for 5 hour EC2 usage.\
Another thing to remember is **Tiered Pricing** which means that the more you use, the less you pay for an unit, e.g. the cost of storing data in S3 decreases as the amount of data stored increases.\
You can use different price models:
* **On-Demand** - standard usage with no strings attached, highest price
* **Reserved Instances** - reserving resources on longer period to pay less for their usage
* **Saving Plans** - flexible commitment to a specific hourly amount \(e.g. $0.05/hour for EC2\), lower costs than On-Demand
* **Spot Instances** - cheap, but can be interrupted by AWS \(ideal for interruption-tolerant workloads\)\
Even if you don't want to pay anything - use **Free Tier** that provides limited access to some services for 12 months.

Main costs sources are:
* Compute services like EC2 and Lambda
* Storage services like S3 and EBS
* Data transfer services like cross-region transfer or traffic egressing AWS
* Managed services like RDS or CloudWatch

Tools for managing costs:
* **Cost Explorer** - visualization of costs over time, filtering by services, tags, regions
* **Budgets** - set budget thresholds and alerts \(e.g. when EC2 spend exceeds 80% of the limit\)\
**How to add Budget?**
1. Navigate to Billing and Cost Management, then to Budgets
2. Choose Create Budget
3. Select if you want to use templates or customize your budget, provide nessesary data.

Tips for optimizing costs:
* Right Sizing - matching the size of resources to actual needs, e.g. select optimal EC2 type
* Auto Scaling - automatic scaling of resources depending on workload
* Resource Tagging - tag resources to analyse costs by projects, environments, etc.
* Alerts - set up alerts for unexpected cost rises
* Automatic resource shutdown - use Lambda + CloudWatch Events to shut down unused dev/test environments after hours
* Remove used resources - EBS volumes without EC2 assignment, Elastic IPs without active instances, forgotten EBS snapshots, etc.
* Use Infracost with IaC to analyse costs
## Networking
### Virtual Private Cloud \(VPC\)
VPC the backbone of your infrastructure. It is a virtual, isolated network inside AWS cloud that is private and fully-configurable for its user. It allows for creating resources isolated from other networks in AWS. Each VPC exists in one region, for different regions there is different VPC.

VPC consists of:
* **CIDR Block** - IP address range that defines address space of VPC.
* **Subnets** - Divided into public and private, they represent IP address range inside VPC. Public subnets have an internet access through Internet Gateway, private subnet do not have direct internet access, they communicate through NAT Gateway. Each subnet is a part of one Availability Zone.
* **Route Tables** - Define how network traffic is routed within a VPC.
* **Internet Gateways** - Allow for communication between resources in VPC and internet.
* **NAT Gateways** - Allow for internet access from private subnets.
* **Security Groups** - Instance-level stateful firewalls that e.g. define open ports, IP addresses that can communicate with instance and allowed protocols.
* **Network ACL** - Subnet-level stateless firewalls that allow or deny traffic from chosen protocol, port range and source IP addresses.
* **VPC Endpoints** - Allow for private access to resources \(e.g. S3 or DynamoDB for free\) without using NAT Gateway or Internet Gateway.

**VPC Peering** is connecting different VPCs \(even from different regions\) without using Internet Gateway.

Best practises:
* **Plan address space** - avoid address conflicts and ensure scalability, use large CIDR ranges \(e.g. /16\) and divide them to subnets
* **Divide on public and private subnets**
* **Multi-AZ** - spread private and public subnets across at least 2 Availability Zones
* **Use Bastion Host** - to access private resources in private subnets, e.g. EC2 inside a public subnet as the only SSH access point to private instances
* **Minimize Internet Exposure** - expose only necessary resources in public subnets
* **Minimize Costs** - use NAT Gateways only when internet access from private subnet is necessary
* **Principle of Least Privilege** - allow for only necessary communication
* **Automate** VPC with IaC!
### CloudFront
CloudFront is a global Content Delivery Network \(CDN\) service that caches and delivers content from Edge Locations. It allows for reducing latency and increasing the speed of content delivery.

CloudFront consists of:
* **Distributions** - configurations that specifies how and where content is distributed, has 2 types: Web Distribution - for HTTP/HTTPS, websites and APIs, RTMP Distribution - for real-time media streaming
* **Edge Locations** - Points of presence \(PoPs\) scattered around the world that cache content close to end users
* **Origin** - source of original content that CloudFront will cache and deliver, it might be e.g. S3 bucket, EC2 instance, ELB or Custom Origin \(e.g. on-premise server\)
* **Cache Behaviors** - rules that define how CloudFront handles different types of requests and content based on URL, they control headers, cookies and URL parameters affecting cache
* **Lambda@Edge** - executing Node.js/Python code on edge locations in response to events \(e.g. request/response modification\)

Best practises:
* **Configure appropriate cache lifetimes \(TTL\) and use file versioning** to optimize efficiency
* **Integrate with WAF** to protect against SQL Injection, XSS attacks, etc.
* **Use HTTPS** \(its obligatory\) with own SSL/TLS certificates or managed by AWS ACM
* **Automatically invalidate cache after new version deployment** to prevent outdated cache
* **Automate** CloudFront with IaC!
### Route 53
Route 53 is DNS \(Domain Name Service\) service. It allows for registering domains and routing network traffic by translating domains to IPs \(normal DNS\) maintaining high availability and scalability. Name comes from DNS port - 53 - and routing network traffic idea.

Route 53 consists of:
* **Hosted Zones** - holds Records Sets, divided on public and private, public - maps domain names to public IP addresses, private - maps domain names inside VPC with internal name resolution
* **Records Sets** - DNS records that define how network traffic is routed, record types below
  * **A** - IPv4 mapping
  * **AAAA** - IPv6 mapping
  * **CNAME** - another domain name mapping
  * **MX** - mail server records
  * **TXT** - text records, usually used for domain ownership verification
  * **ALIAS** - native AWS alias, directly maps to resources \(e.g. maps to ALB, S3, CloudFront\)
* **Routing Policies** - define how network traffic should be routed, policies types below
  * **Simple Routing** - default routing \(one record -> one resource\)
  * **Weighted Routing** - routes network traffic based on assigned weights \(e.g. 70% to region A, 30% to region B\)
  * **Latency-Based Routing** - routes network traffic to resource with lowest latency
  * **Failover Routing** - routes network traffic to backup resource in case of failure of the main one
  * **Geolocation Routing** - routes network traffic based on user's geographic location
* **Health Checks** - resource status monitoring mechanism that automatically redirects network traffic to healthy resources

Best practises:
* **Use Health Checks** to avoid routing to unhealthy resources, try to implement automatic fix procedures \(with e.g. Lambda\)
* **Avoid using CNAME for main domain**, instead use ALIAS - cheaper and faster
* **Adjust Time to Live \(TTL\) value**, short \(e.g. 60s\) for dynamically changed domains, long \(24h\) for stable records
* **Separate environments with Hosted Zones**, e.g. prod/dev
* **Use Geolocation Routing for EU users to direct them to GDPR-compliant regions**
* **Automate** Route 53 with IaC!
### Elastic Load Balancing \(ELB\)
ELB is, like name suggests, a load balancer - service that automatically distributes incoming network traffic across multiple destinations to provide high availability, scalability and proper efficiency of application.

ELB types:
* **Application Load Balancer \(ALB\)** - works in the application layer \(7th - ISO-OSI\), best for balancing HTTP/HTTPS traffic, routing is based on URL paths, hosts and HTTP headers, suits web applications
* **Network Load Balancer \(NLB\)** - works in the transport layer \(4th - ISO-OSI\), best for balancing TCP/UDP traffic, works with static IP and maintains source IP of client, suits applications that need high throughput and low latency \(e.g. online games or VoIP apps\)
* **Gateway Load Balancer \(GWLB\)** - traffic routed through virtual appliances in VPC
* **Classic Load Balancer \(CLB\)** - legacy!, works in application and transport layer, balances both HTTP/HTTPS and TCP/UDP traffic

ELB consists of:
* **Listeners** - defines ports and protocols that load balancer will listen for, for ALB also defines routing rules - explanation below
  * **Routing Rules** - divides on: Path-based routing - routes by URL path, Host-based routing - routes by host, Redirects/Forwarding - HTTP -> HTTPS redirects
* **Target Groups** - defines instances, containers or IP addresses to which the load balancer routes traffic
* **Health Checks** - target status monitoring mechanism that determines whether a target is able to handle traffic, unhealthy targets are excluded from load distribution

Best practises:
* **Use Health Checks** to avoid routing to unhealthy resources
* **Use correct type of load balancer for its purpose**
* **Deploy across multiple Availability Zones and use Cross-Zone Load Balancing \(automatic traffic distribution across AZs\)**
* **Integrate with WAF** to protect from SQL Injection and XSS attacks
* **Use SSL/TLS Termination** to enable load balancer to handle SSL decryption, offloading the backend
* **Integrate with Auto Scaling** to automatically add or remove instances in response to load
* **Automate** ELB with IaC!
### API Gateway
API Gateway is a managed service for creating, exposing, maintaining, monitoring and securing APIs at internet scale. It ensures high availability, scalability and security of APIs.

API types:
* **REST API** - traditional HTTP/REST applications
* **HTTP API** - simplified version of REST API with lower costs \(e.g. integration with Lambda\)
* **WebSocket API** - two-way real-time communication \(e.g. chats, games\)

API Gateway consists of:
* **Resources** - resources on which operations can be performed, each resource can have one or more HTTP methods, defined as URL paths
* **Methods** - HTTP methods that define operations that can be performed on resources
* **Stages** - API versions \(e.g. environment versions - prod/dev\), each stage has its own URL path and configuration, stage variables are environment variables of stage
* **Deployments** - process of publishing API changes to a specific stage, supports rollbacks and canary deployments
* **Integration** - integrate with e.g. Lambda, HTTP \(any endpoint\) or AWS Services as backend

Best practises:
* **Use Stages** to separate environments
* **Use Authorization \(e.g. AWS Cognito\), Throttling, WAF Integration and HTTPS** to secure and protect API from attacks
* **Use Cache** to optimize API efficiency
* **Use CORS properly**
* **Use Velocity Template Language \(VTL\)** to convert formats \(e.g. XML -> JSON\)
* **Automate** API Gateway with IaC!
## Computing
### Elastic Compute Cloud \(EC2\)
EC2 is a virtual server service in cloud. It allows for flexible customization of instances, integration with other services and scaling instances if needed \(see Auto Scaling\).

EC2 offers many types of instances, different types are differently optimized:
* **General Purpose** - balanced CPU/RAM, e.g. T4g, suits web applications
* **Compute Optimized** - high CPU performance, e.g. C7g, suits batch computing
* **Memory Purpose** - large amount of RAM, e.g. R6i, suits databases, analytics
* **Accelerated Computing** - GPU/TPU, e.g. P4, suits machine learning
* **Storage Purpose** - high disk throughput, e.g. I4i, suits Big Data

EC2 instances need to have configured a Amazon Machine Image \(AMI\) which contains operating system and desired configuration for created instance, e.g. Amazon Linux.\
To securely connect to EC2 instance using SSH you need to create SSH key pair, you can create one while creating instance.\
EC2 instance needs storage, it uses EBS that can be configured while creating instance.\
You can run some configuration at creating EC2 instance - use User Data to provide configuration script.\
To provide static IP address for an instance use Elastic IP, but remember to remove unused Elastic IPs!

Best practises:
* **Apply Immutable Infrastructure** - instead of updating instances, replace them with new ones \(AMI + AS\)
* **Distribute instances across multiple AZs** - to assure high availability
* **Use EBS snapshots for critical data**
* **Apply Security Hardening** by switching off unused ports, updating OS and using minimal permissions
* **Use Auto Scaling** for adjusting number of instances to the workload
* **Optimize costs** using Spot Instances, Reserved Instances, etc. described in Billing & Costs
* **Automate** EC2 with IaC!
### Auto Scaling
Auto Scaling is a service that automatically adjusts the number of services \(EC2, ECS, DynamoDB\) instances to the workload. It provides high availability and scalability of application and cost optimization of used resources.

Auto Scaling consists of:
* **Launch Template/Configuration** - template containing the configuration of new instances to be launched by Auto Scaling
* **Auto Scaling Group** - group of instances that are managed by Auto Scaling, takes parameters of minimum, maximum and desired number of running instances
* **Scaling Policies** - policies determine how Auto Scaling adjusts the number of instances, types below
  * **Target Tracking Scaling** - maintains a specific metric \(e.g. 60% CPU average usage\)
  * **Step Scaling** - adds/removes instances by steps \(e.g. +2 instances at CPU average usage > 80%\)
  * **Simple Scaling** - responds to a CloudWatch alarm with a simple action \(e.g. +1 instance\)
* **Health Checks** - scaled instances status monitoring mechanism

Best practises:
* **Maintain some instances free for unexpected traffic spikes**
* **Mix or use Spot Instances** to optimize costs if application can tolerate outages
* **Use Termination Policies** to define which instances to delete first
* **Execute scripts before instance deletion/launch** to e.g. drain traffic or initialize data
* **Test scaling** with AWS Load Testing
* **Use Instance Refresh** to automatically replace instances, e.g. when AMI is updated
* **Integrate with ELB** to balance traffic incoming to instances
* **Distribute instances across multiple AZs** - to assure high availability
* **Avoid under-scaling and over-scaling** by correct scaling policies
* **Automate** Auto Scaling with IaC!
### Elastic Beanstalk
Elastic Beanstalk is a service that automates deployment, scaling, monitoring and managing applications. Its purpose is to simplify operations for developers without need to manage infrastructure, automating its managing. It supports multiple platforms \(Java, .NET, Python, Node.js, PHP, Go, Ruby\) and integrates other services \(e.g. like EC2, RDS, S3, CloudWatch, Auto Scaling\).

Elastic Beanstalk consists of:
* **Applications** - logical set of environments, versions and configurations
* **Environments** - specific application instances working on defined infrastructure configuration, they divide to 2 tiers explained below
  * **Web Server Tier** - supports services based on handling HTTP/HTTPS requests
  * **Worker Tier** - processing tasks in the background, \(e.g. SQS queues\)
* **Application Versions** - specific application version that can be deployed to an environment
* **Environment Configurations** - environment settings configuration \(e.g. instance type, auto scaling and environment variables\)

Elastic Beanstalk offers different deployment strategies:
* **All at Once** - instant replacement of all instances, causes downtime so not so good
* **Rolling** - gradual update of instances in groups, minimizes downtime
* **Blue/Green** - deploying a new version in a parallel environment + redirecting traffic
* **Immutable** - secure deployment by creating new instances and replacing old ones

Best practises:
* **Use HTTPS** with SSL/TLS certificates \(e.g. with ACM\)
* **Use Auto Scaling and ELB** to assure high availability
* **Customize infrastructure with .ebextensions**
* **Use Platform Hooks** to run scripts at different life cycle states \(e.g. predeploy or postdeploy\)
* **Automate** Elastic Beanstalk with IaC!
### Lambda
Lambda is a serverless compute service that runs code in response to events \(e.g. HTTP request or S3 change\) and automatically manages compute resources without need to manage infrastructure. It scales automatically and integrates with other services.

Lambda consists of:
* **Functions** - code that is ran in response to events, different runtimes are supported \(e.g. Node.js, Python, .NET, Java, Go or even custom\), configuration by environment variables, layers are used to reuse code by sharing libraries and other dependencies between multiple Lambdas
* **Triggers/Events** - events that trigger function execution, related to other resources \(e.g. S3, API Gateway, etc.\) or external events \(e.g. HTTP request\)

Best practises:
* **Use Provisioned Concurrency to reduce cold starts**
* **Use aliases and versions** to keep versions in order
* **Keep stateless and idempotent design**
* **Automate** Lambda with IaC!
### Elastic Container Service \(ECS\)
ECS is a managed container orchestration service that allows for running, scaling and monitoring containerized applications. It automatically scales number of containers in response to changing network traffic, integrates with Docker and other services.

ECS consists of:
* **Clusters** - resources \(EC2 instances or Fargate resources\) that run tasks and services
* **Tasks Definitions** - configuration of tasks, defines e.g. Docker image, resources \(CPU and memory\), environment variables, IAM roles, network and volumes
* **Tasks** - single container launch \(or multiple containers in one task\)
* **Services** - manages the number of tasks instances in a cluster to ensure high availability and scalability, automatically registers tasks in the load balancer
* **Launch Types** - how containers are launched, types below
  * **EC2 Launch Type** - containers are launched on user-managed EC2 instances
  * **Fargate Launch Type** - containers run on AWS-managed infrastructure, without the need to manage servers

Best practises:
* **Use Fargate to keep it simple, use EC2 to keep it low-cost**
* **Use multiple AZs** to assure high availability
* **Use Provisioned Capacity to reduce cold starts**
* **Automate** ECS with IaC!
### Elastic Kubernetes Service \(EKS\)
EKS is a managed Kubernetes cluster service that allows for running, scaling and managing containerized applications. It supports native k8s API, maintains scalability, integrates with other services and takes care of the availability, updates, and security of the control layer \(API Server, etcd, scheduler\).

EKS consists of:
* **Clusters** - groups of nodes that run containerized applications managed by Kubernetes
* **Nodes** - Physical or virtual machines on which containers run, different node managing types below
  * **Managed Node Groups** - managed groups of nodes that automatically manage updates and scaling
  * **Self-Managed Nodes** - user-managed nodes that require manual configuration and management
  * **Fargate** - containers are run on managed infrastructure without the need to manage nodes
* **Kubernetes Objects** - Pods, Deployments, Services, Ingresses, etc.

Use VPC CNI Plugin to integrate k8s network with VPC, each pod has its own IP from the VPC subnet.\
Use IAM Roles for Service Accounts \(IRSA\) to authorize pods for services.

Best practises:
* **Use Cluster Autoscaler or Karpenter** to automatically adjust the number of nodes based on traffic
* **Use Managed Node Groups if you want automatic AMI and Kubelet version updates**
* **Backup cluster state** e.g. with Velero
* **Right-size** cluster nodes to handled traffic
* **Automate** EKS with IaC!
## Storage
### Simple Storage Service \(S3\)
S3 is a object storage service that allows for high availability, security and endless scalability of storage. It integrates with nearly all of services.

S3 consists of:
* **Buckets** - containers for objects, named with globally unique names, created inside specified region, versioning object changes
* **Objects** - files + metadata \(up to 5TB\), identified by key \(path\)
* **Storage Classes** - types of storage that differ in availability, access time and costs, types below
  * **S3 Standard** - highly available, for frequent access, high costs
  * **S3 Intelligent-Tiering** - automatically transfers data between layers based on usage frequency
  * **S3 Standard-IA** - for rare access \(e.g. backups\), less available and cheaper than standard
  * **S3 One Zone-IA** - for volatile data, stored in only one AZ, low availability and costs
  * **S3 Glacier Instant** - for archives \(min. 90 days\) with fast access, low costs
  * **S3 Glacier Deep** - for deep, long-term archives \(min. 180 days\), lowest availability and costs

Access to buckets should be controlled - **use IAM and Bucket Policies**.\
**Encrypt** stored data and during communication with buckets, **log** bucket actions.\
Use S3 **Event Notifications** to automatically run Lambda, SQS, SNS actions in response to bucket changes.\
Use **Lifecycle Policies** to automatically transfer data between storage layers or remove old versions.\
Use **Cross-Region Replication \(CRR\) and Same-Region Replication \(SRR\)** to replicate data to assure their availability and durability.\
Use **s3-accelerate.amazonaws.com \(Transfer Acceleration\) and Multipart Upload** for faster large file transfer, use **S3 Select & Glacier Select** to extract only needed data from large files.\
Use **S3 Storage Lens** for advanced metrics analysis and cost optimization.

Best practises:
* **Use bucket naming convention and tags** to keep it consistent
* **Use CORS** to configure access headers
* **Use Presigned URLs** for temporary URLs for download/upload
* **Block public access** for safety
* **Automate** S3 with IaC!
### Glacier
Glacier is a storage service for long-term data archiving. It suits rarely accessed data in exchange for low costs of storing.

Glacier consists of:
* **Vaults** - container for archives \(analogous to S3 bucket\)
* **Archives** - single object \(file, zip, backup\) up to 40 TB in size
* **Jobs** - process that initiates the download of the archive, types based on download speed below
  * **Expedited** - 1–5 minutes \(additional charge\)
  * **Standard** - 3-5 hours
  * **Bulk** - 5-12 hours \(cheapest for large collections\)
 
Glacier offers different layers of storing:
* **Instant Retrieval** - for fast access \(ms\), but higher costs, minimum 90 days of storing
* **Flexible Retrieval** - for average access \(minutes to hours\) with average costs, minimum 90 days of storing
* **Deep Archive** - for low costs, but longer access time \(hours\), minimum 180 days of storing

Use **Vault Lock Policies** to force compliance rules \(e.g. WORM – Write Once Read Many\).

Best practises:
* **Avoid small files, merge into larger archives** for reducing costs
* **Use 3-2-1 backup strategy** and store one backup in Glacier
* **Automate** Glacier with IaC!
### Elastic Block Store \(EBS\)
EBS is a managed block storage service for EC2 instances, providing persistent network drives.

EBS consists of:
* **Volumes** - virtual disks connected to EC2 instances, types below
  * **gp3/gp2 \(General Purpose\)** - universal SSD drive for e.g. file systems or web applications, low latency, high throughput, best price-performance ratio, average Input/Output Operations Per Second \(IOPS\)
  * **io1/io2 \(Provisioned IOPS\)** - high performance for demanding workloads \(e.g. OLTP databases\), lowest latency, high throughput, high costs, high IOPS
  * **st1 \(Throughput Optimized\)** - HDD drive with high throughput and average latency, throughput and costs, low IOPS
  * **sc1 \(Cold HDD\)** - for rarely used data, high latency, low throughput, low costs, lowest IOPS
* **Snapshots** - incremental volume backups stored in S3, can use Cross-Region Snapshots for assuring availability and durability

Volumes type and size can be dynamically changed on-the-fly \(except io1 -> io2\).\
io1/io2 can be attached to multiple instances.\
EBS snapshots can be used for building preconfigured AMIs.\
Configure RAID 0/1 on EC2 for increased performance/redundancy.

Best practises:
* **Use Lifecycle Manager** for automatic creation and deletion of snapshots
* **Right-size** by choosing proper type and size
* **Replicate to Multiple AZs** to assure high availability
* **Automate** EBS with IaC!
### Elastic File System \(EFS\)
EFS is a managed, scalable network file service \(NFS\) for EC2 instances, containers in ECS/EKS and Lambda functions. It works in a shared storage model, enabling data sharing across multiple instances and services in real time. It automatically scales its size, is available in multiple AZs and uses NFSv4.

EFS offers different performance modes:
* **General Purpose** - for standard workloads \(e.g. web applications\)
* **Max I/O** - for high parallelism \(thousands of NFS connections, e.g. big data\)

EFS offers different throughput modes:
* **Bursting** - throughput scales with system size \(e.g. 1 TB = 50 MB/s burst\)
* **Provisioned** - consistent throughput regardless of size \(e.g. 100 MB/s\)

EFS offers different storage classes:
* **Standard** - for frequent access
* **Infrequent Access \(IA\)** - cheaper, for rarely used data

EFS consists of:
* **File Systems** - store data in a directory and file structure, works in one region but many AZs
* **Mount Targets** - mount points in VPC subnets \(one per AZ\) that allow EC2 instances to access EFS via NFS, instance **2049 port** must be opened for NFS traffic
* **Access Points** - access points with predefined paths \(root directory\), POSIX and IAM permissions

Best practises:
* **Use Lifecycle Manager** for automatic data transfer to IA storage class after e.g. 30/60/90 days
* **Use AWS Backup** to create EFS snapshots
* **Use User Data parameter in instance template in Auto Scaling** for automatic EFS mounting
* **Use Mount Targets in multiple AZs** to assure high avaiability
* **Automate** EFS with IaC!
### Elastic Container Registry \(ECR\)
ECR is a managed Docker container image registry that hosts and manages them. It integrates with every service that uses containers.

ECR consists of:
* **Repositories** - private or public Docker images collections with multi-architecture support, one repository exists in one region, replicate between regions if needed
* **Images** - versioned artifacts by using tags
* **Image Scanning** - automatic detection of vulnerabilities \(CVE\) in image layers
* **Lifecycle Policies** - automatic images deletion \(e.g. keep last 10 tags, delete images older than 30 days\)

Best practises:
* **Avoid latest tag** to keep it consistent
* **Use Immutable Tags** for production environments
* **Use ECR Pull Through Cache** for image caching to reduce transfer costs
* **Automate** ECR with IaC!
## Databases
### Relational Database Service \(RDS\)
RDS is a managed relational database service that supports many popular relational engines \(e.g. MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Amazon Aurora\). It manages instalation, patches, backups, scaling of database and assures its high availability.

To assure high availability and scalability RDS applies:
* **Multi-AZ Deployment** - automatic synchronous replication to standby instance in another AZ \(failover in <2 min\)
* **Read Replicas** - read-only replicas to offload the primary database \(supported within a region or cross-region\)
* **Vertical and Horizontal Scaling** - vertical: changing the instance type \(e.g. from db.t3.medium to db.r5.large\), horizontal: adding read replicas

RDS integrates with IAM to authenticate database users and management operations.\
Use **Automated Backups and Point-in-Time Recovery** to run automated, regular db backups \(e.g. daily\) stored up to 35 days and restore the database to any point within the retention period.\
You can also run standard **Manual Snapshots** stored in S3.\
You can use Aurora to automatically scale capacity to 128 TB based on workload.\
You can **Reserve Instances** for 1 or 3 years to pay less.\

Best practises:
* **Avoid public access** for safety
* **Use Automated Backups** to prevent losing data
* **Right-size** storage and class to expected workload and data size
* As you should always, **encrypt data \(with KMS\) and  use SSL/TLS for connections**
* **Automate** RDS with IaC!
### DynamoDB
DynamoDB is a managed NoSQL database service that offers low latency and automatic scalability. It stores data in key-value or document model.

DynamoDB consists of:
* **Tables** - classic data collections, they contain: attributes - fields, Partition Keys - uniquely identifies an element and distributes data across partitions, Sort Keys - optional, enables sorting and range queries
* **Global Secondary Indexes \(GSI\)** - additional indexes with their own partition/sort keys, asynchronously updated
* **Local Secondary Indexes \(GSI\)** - indexes that share the partition key with the table

DynamoDB offers different capacity modes:
* **Provisioned** - manual RCU/WCU \(read/write units\) setting, with auto-scaling option
* **On-Demand** - auto-scaling, pay-per-request

You can perform different actions to access database:
* **PutItem/GetItem** - save/read single item
* **Query** - get items from one partition \(efficient\)
* **Scan** - search entire table

Like in RDS, you can automate backups and use Point-in-Time Recovery.\
To enhance failure resistance use **Global Tables** - cross-region data replication \(multi-master\).
Use **DynamoDB Streams** to detect changes in data and use it in Event-Driven Architecture.

Best practises:
* **Avoid using Scan action** because it is costly and slow - design queries using Query and indexes
* **Use Time-to-Live \(TTL\)** to automatically remove old data \(user sessions\)
* As you should always, **encrypt data \(with KMS\) and  use SSL/TLS for connections**
* **Use DynamoDB Accelerator \(DAX\)** to enable read cache
* **Avoid hot partition** by using high cardinality partition key \(e.g. UserID instead of Status\)
* **Use partition + sort key combination** for range queries
* **Automate** DynamoDB with IaC!
### ElastiCache
ElastiCache is a managed in-memory cache service that speeds applications by storing frequently used data in RAM. It supports Redis and Memcached engines.

Memcached engine:
* Works as a single cache server with simple key-value model
* Can be horizontally scaled
* Data is ephemeral \(will dissapear after some time\), so no replication
* Auto-Discovery - automatic node discovery by clients
* Partitioning - distributing data across nodes

Redis engine:
* Can work in Cluster Mode - a group of nodes where Primary Node processes reads and writes and Replica Nodes only writes.
* Sharding - sharing data across multiple nodes
* Persistence - optional data storage on disk
* Multi-AZ replication with auto failover
* Support for data structures \(lists, sets\)

Both engines need configuration \(e.g. maxmemory-policy, timeout\).

Best practises:
* **Choose right engine** - Redis for durability, replication, or data structures, Memcached for simple scenarios with horizontal scaling
* **Optimize Time-to-Live \(TTL\)** to avoid memory overflow
* As you should always, **encrypt data \(with KMS\) and  use SSL/TLS for connections**
* **Automate** ElastiCache with IaC!
## Security
### Cognito
Cognito is a managed AWS service for authentication, authorization, and user management in applications.

Cognito consists of:
* **User Pools** - user directories for registration, login and profile management \(authentication\)
* **Identity Pools** - service that issues users **temporary** AWS credentials \(e.g. access to S3, DynamoDB\) based on their identity \(authorization\)

User Pools functions:
* **Registration/Login** - supports login via email, phone number, social media \(Google, Facebook, SAML/OIDC\)
* **Password policy enforcement** - e.g. minimum length, special characters
* **Multi-Factor Authentication \(MFA\)**
* **Adaptive Authentication** - detection of unusual logins \(e.g. from a new location\)
* **Hosted UI** - ready-made login interface with customizable CSS
* **User Migration** - import existing accounts while preserving passwords

Identity Pools functions:
* **Authorization to access resources** - generating Temporary AWS Keys \(STS\) for IAM Roles, support for non-logged-in users \(optional\)
* **Integration with User Pools** - combining Authentication and Authorization
* **Federated Identity** - binds logins from User Pool, social providers or Amazon

Cognito also supports JWT tokens:
* **ID Token** - contains user data \(e.g. email, groups\)
* **Access Token** - used to authorize requests to the backend
* **Refresh Token** - refreshes token validity without re-logging in

Cognito integrates with services that may need authorization \(e.g. API Gateway, Lambda\), web and mobile applications and services to which access should be controlled \(e.g. S3, DynamoDB\).

Best practises:
* **Validate and Configure JWT Tokens** - always validate JWT signature and audience \(aud/client_id\) and configure id_token and access_token \(default 1 hour\)
* **Use MFA** for critical applications
* **Automate** Cognito with IaC!
### Key Management Service \(KMS\)
KMS is a managed service for creating and controlling cryptographic keys used to encrypt data in AWS. It allows for generating and storing keys, automatic key rotation and integrates with most of services.

KMS consists of:
* **Key types** - types of keys that can be managed, types below
  * **AWS-Managed Keys** - keys automatically created by AWS for services \(e.g. S3, EBS\)
  * **Customer-Managed Keys \(CMK\)** - user managed keys \(full control over policies and rotation\)
  * **Data Keys** - keys generated for encrypting large data sets \(so-called envelope encryption - Data Keys are encrypted by generated CMK key\)
* **Key Policies** - JSON defining who can manage/use keys \(e.g. IAM roles, AWS accounts\)

For **S3** enable SSE-KMS encryption for buckets.\
For **EBS** encrypt volumes with CMK.\
For **Lambda** encrypt environment variables with KMS key.\
For **RDS** encrypt databases during their creation.\
Integrate with **TLS** for in-transit encryption.\
Store encrypted secrets in **Secrets Manager**, see below.

Best practises:
* **Use human-friendly aliases** instead of key id
* **Use Automatic Rotation** for keys, e.g. rotation every year, old versions of keys are stored for data decryption
* **Block key deletion** to prevent accidental key deletion
* **Automate** KMS with IaC!
### Secrets Manager
Secrets Manager is a managed service for secure storage and rotation of secrets \(e.g. Database passwords, API keys, TLS certificates, Access Tokens\).

Secrets Manager can automatically rotate secrets, it has built-in support for RDS, Redshift, DocumentDB \(rotation every X days\) and supports creating own rotation functions for any secret.\
Stored secrets are encrypted with KMS keys \(default by AWS-Managed Key, but CMK can be used\) and versioned.\
Service integrates with other services that need to use sensitive data.

Best practises:
* **Use rotation** for sensitive secrets
* **Principle of Least Privilege** - grant only *secretsmanager:GetSecretValue* access to application roles
* **Automate** Secrets Manager with IaC!
### Certificate Manager \(ACM\)
ACM is a managed service that allows you to easily create, store, and renew SSL/TLS certificates. These certificates are used to secure communication between the client and the server \(e.g. HTTPS for websites, APIs, load balancers\). Service offers free public certificates for servces \(e.g. CloudFront, ALB, API Gateway, Cognito\) and automatically renews certificates \(with no risk of expiration\). Encrypted certificates are stored in KMS.

Certificates divide on public and private. Public certificates are used for securing public domains, private are used for securing internal services inside VPC \(e.g. communication between microservices\).\
Domain ownership is verificated by 2 methods: DNS method - adding CNAME record to DNS \(e.g. Route 53\), recommended for automation and Email method - manual confirmation by domain owner, less recommended for automation.\
You can use Subject Alternative Names \(SANs\) to add multiple domains to one certificate.

Best practises:
* **Use DNS verification method** to automate verification
* **Use correct region for certificate** - e.g. CloudFront requires certificates from us-east-1 region
* **Automate** ACM with IaC!
### Web Application Firewall \(WAF\)
WAF is a managed firewall service that protects web applications from common attacks such as SQL injections, XSS, DDoS and Bad Bots, it works at the application layer \(7\). It integrates with services that need protection \(e.g. CloudFront, ALB, API Gateway\).

WAF consists of:
* **Web ACLs \(Access Control Lists\)** - set of rules that determine which requests are blocked or allowed
* **Rules** - specified conditions when requests are allowed, blocked or count, they can involve: IP match \(IP addresses and CIDR ranges\), String Match \(patterns in headers, body or URI\), Regex \(advanced matches\), Geographic Match \(geographic location\), types below
  * **Custom Rules** - user-defined rules
  * **Managed Rules** - predefined rules
  * **Rate-Based Rules** - rules that automatically block IP addresses sending too many requests in a short period of time \(e.g. >100 requests/5 minutes\)
* **Rule Groups** - sets of rules, types below
  * **Managed Rule Groups** - ready-made rulesets from AWS or partners \(e.g. AWS Managed Rules for OWASP Top 10\)
  * **Custom Rule Groups** - custom rules created by yourself
* **IP Sets** - IP Allow/Block Lists

Best practises:
* **Use Managed Rules** to protect services from OWASP Top 10, bots or scanners
* **Use Custom Rules** for specific needs \(e.g. block known malicious IP addresses, detect unusual attacks\)
* **Rate limit** login endpoints \(e.g. /login\) to prevent brute-force attacks
* **Use rules hierarchy** by configuring rules priorities
* **Automate** WAF with IaC!
## Monitoring
### CloudWatch
CloudWatch is a monitoring and observability service that collects metrics, logs, and events from cloud resources and applications.

CloudWatch consists of:
* **Metrics** - desired mesaures of specified resources, divided on standard - automatically collected from services \(e.g. EC2, RDS, Lambda\), and custom - sent by applications \(e.g. number of active users\), metrics can be grouped into namespaces
* **Logs** - storage and analysis of application logs, can be organised into Log Groups and use Log Streams - single log streams within a group, CloudWatch Logs Insights can be used for SQL-like query log analysis
* **Alarms** - automatic notifications and actions when metric thresholds are exceeded
* **Dashboards** - configurable dashboards with widgets \(metrics, logs, charts\)
* **EventBridge** - reactions to changes in cloud \(e.g. EC2 startup, Lambda error\) via AWS EventBridge

Key metrics for example services:
* **EC2** - CPUUtilization, FreeStorageSpace, NetworkIn/Out, StatusCheckFailed
* **RDS** - CPUUtilization, NetworkIn/Out, FreeStorageSpace, DatabaseConnections
* **Lambda** - Errors, Duration, ConcurrentExecutions, Throttles, Invocations
* **S3** - NumberOfObjects, BucketSizeBytes

You can integrate CloudWatch with Prometheus/Grafana by using CloudWatch Data Source in Grafana.

Best practises:
* **Always monitor and observe services and resources** to keep everything in tact and working
* **Set up alerts** that send notifications and take actions to not to be surprised by unexpected failures
* **Structure logs** by using JSON and Embedded Metric Fromat \(EMF\)
* **Automate** CloudWatch with IaC!
## Integration & Messaging
### Simple Queue Service \(SQS\)
SQS is a managed message queue service that enables asynchronous communication between application components \(microservices, servers, Lambda functions\). It offers high throughput: Up to 3,000 messages/second \(Standard\) and 300/second \(FIFO\). It integrates with some services \(e.g. Lambda\) and supports Event-Driven Architecture.

SQS consists of:
* **Queues** - main component of the service, types below
  * **Standard** - at-least-once delivery guarantee, no message order, high throughput
  * **First-In-First-Out \(FIFO\)** - exactly-once processing, strict message order, lower throughput than Standard
* **Messages** - main object going through queue, up to 256 KB of text, metadata can be added, Time-to-Live \(TTL\) can be set to specify how long message will stay in queue
* **Visibility Timeout** - amount of time a message is invisible to other consumers after being taken from queue \(default 30 seconds\), if it is not deleted, it will return to the queue
* **Dead-Letter Queue \(DLQ\)** - queue for messages that failed to process after a specified number of attempts \(maxReceiveCount\)

Best practises:
* **Use Standard type for high throughput and FIFO when order and no duplicates are crucial**
* **Batch message sending** for costs optimization
* **Use Long Polling** to reduce empty responses \(ReceiveMessage waits up to 20 seconds for messages to appear\)
* **Automate** SQS with IaC!
## LocalStack
LocalStack is a tool that emulates AWS services in a local environment, making development and testing possible without access to the AWS cloud.

LocalStack emulates popular AWS services \(e.g. Lambda, EC2, S3, DynamoDB, SQS, API Gateway, CloudFront, CloudWatch, IAM\).\
LocalStack runs as a set of Docker containers and is configured to run services with Docker Compose or environment variables \(e.g. SERVICES=s3,lambda\).\
LocalStack integrates with Terraform and AWS CLI, both can be used to manage local infrastructure emulation.

[https://www.localstack.cloud/](https://www.localstack.cloud/)
