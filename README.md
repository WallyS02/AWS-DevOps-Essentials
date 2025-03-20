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
