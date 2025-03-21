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

### API Gateway
## Computing
### Elastic Compute Cloud \(EC2\)
### Auto Scaling
### Lambda
### Elastic Container Service \(ECS\)
### Elastic Kubernetes Service \(EKS\)
### Elastic Beanstalk
## Storage
### Simple Storage Service \(S3\)
### Elastic Block Store \(EBS\)
### Elastic File System \(EFS\)
### Elastic Container Registry \(ECR\)
### Glacier
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
