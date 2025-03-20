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
### VPC
### CloudFront
### Route 53
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
### Web Application Firewall \(WAF\)
## Monitoring
### CloudWatch
## Integration & Messaging
### Simple Queue Service \(SQS\)
## LocalStack
## Akeero
