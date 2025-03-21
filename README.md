# AWS-DevOps-Essentials
## If I'll find any other feature to be useful - it'll surely be added!
## Basics
AWS is a cloud platform that offers many networking, computing, storage, etc. services. It allows for building scalable, flexible and automated IT infrastructure.
### Regions & Availability Zones
#### Regions
Regions are physical locations in the world where AWS hosts their data centers. Each region consists of separate and isolated Availability Zones. Regions are independent of each other and may differ e.g. in laws \(e.g. RODO in Europe\). When creating infrastructure remember to choose regions close to end users of infrastructure, it minimizes latencies.\
Some regions examples:
* use-east-1 - Northern Virginia, USA
* eu-west-1 - Ireland
* ap-southeast-1 - Singapore
#### Availability Zones
Availability Zones are one or more physical data centers located in region. Their infrastructure is isolated from each other, but they are connected by low-latency, private networks. Thanks to this availability zones are not affected by each others failures. You can replicate data and balance network traffic to different availability zones in region to ensure high availability and efficiency.
### Identity and Access Management \(IAM\)
IAM manages access to resources and services in cloud. Access is based on created users and their assigned groups, roles and permissions. Thanks to this you can strictly control who and what can do in your cloud.

**Users** are individual persons or applications that can access and use cloud. \
**Groups** are sets of users that can be assigned common permissions.\
**Roles** are sets of permissions that can be assigned to users.\
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
### Lambda
### Elastic Container Service \(ECS\)
### Elastic Kubernetes Service \(EKS\)
### Fargate
## Storage
### Simple Storage Service \(S3\)
### Elastic Block Store \(EBS\)
### Elastic File System \(EFS\)
### Glacier
### Elastic Container Registry \(ECR\)
## Databases
### Relational Database Service \(RDS\)
### DynamoDB
### ElastiCache
## Security
### Key Management Service \(KMS\)
### Secrets Manager
### Certificate Manager \(ACM\)
### Web Application Firewall \(WAF\)
## Monitoring
### CloudWatch
## Integration & Messaging
### Simple Queue Service \(SQS\)
## LocalStack
## Akeero
