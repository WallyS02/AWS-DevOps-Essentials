# AWS-DevOps-Essentials
## If I'll find any other feature to be useful - it'll surely be added!
## Basics
AWS is a cloud platform that offers many networking, computing, storage, etc. services. It allows for building scalable, flexible and automated IT infrastructure.
### Regions & Availability Zones
#### Regions
Regions are physical locations in the world where AWS hosts their data centers. Each region consists of seperate and isolated Availability Zones. Regions are independent of each other and may differ e.g. in laws \(e.g. RODO in Europe\). When creating infrastructure remember to choose regions close to end users of infrastucture, it minimizes latencies.\
Some regions examples:
* use-east-1 - Northern Virginia, USA
* eu-west-1 - Ireland
* ap-southeast-1 - Singapore
#### Availability Zones
Availability Zones are one or more physical data centers located in region. Their infrastructure is isolated from each other, but they are connected by low-latency, private networks. Thanks to this availability zones are not affected by each others failures. You can replicate data and balance network traffic to different availability zones in region to ensure high availability and efficiency.
### Identity and Access Management \(IAM\)
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
