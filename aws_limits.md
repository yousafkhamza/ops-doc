### **1. Amazon VPC (Virtual Private Cloud)**
- **Maximum VPCs per Region**: 5
- **Maximum Subnets per VPC**: 200
- **Maximum IP Address Range per Subnet**: /16 (65,536 IP addresses)
- **Maximum Route Tables per VPC**: 200
- **Maximum Routes per Route Table**: 100 (static routes)
- **Maximum Network ACLs per VPC**: 200
- **Maximum Security Groups per VPC**: 500
- **Maximum Security Groups per ENI (Elastic Network Interface)**: 16

---

### **2. Amazon S3 (Simple Storage Service)**
- **Maximum Object Size**: 5 TB (with multipart upload)
- **Maximum Number of Objects per Bucket**: No limit (object count is virtually unlimited)
- **Maximum Bucket Name Length**: 63 characters
- **Maximum Bucket Count per Account**: 100 (can request higher limits)
- **S3 Bucket Versioning**: Unlimited versions
- **Maximum Object Key Length**: 1,024 bytes
- **Maximum PUT Object Request Size**: 5 GB (for single PUT)
- **S3 Transfer Acceleration Max Speed**: Up to 200 Mbps (based on network conditions)

---

### **3. Amazon EC2 (Elastic Compute Cloud)**
- **Maximum EC2 Instances per Region**: 20 (default, request limit increase if needed)
- **Maximum Elastic IPs per Region**: 5
- **Maximum Security Groups per EC2 instance**: 5
- **Maximum Instance Types per Region**: Dependent on instance types but can request an increase.
- **Maximum EBS Volumes per Region**: 1,000 (general limit)
- **Maximum EBS Volume Size**: 16 TB

---

### **4. Amazon Route 53 (DNS Service)**
- **Maximum Hosted Zones per AWS Account**: 500 (default, can request increase)
- **Maximum Records per Hosted Zone**: 10,000 (default)
- **Maximum Resource Record Sets per Hosted Zone**: 10,000 (default)
- **Maximum Health Checks per Account**: 1,000
- **Maximum Alias Records per Hosted Zone**: 10,000
- **Maximum Geolocation Resource Records per Hosted Zone**: 100

---

### **5. Elastic Load Balancer (ELB)**
- **Maximum Load Balancers per Region**: 50 (for each type of load balancer)
- **Maximum Listeners per Load Balancer**: 25 (ALB, NLB, and Classic ELB)
- **Maximum SSL Certificates per ALB**: 25
- **Maximum Targets per Target Group**: 1,000

---

### **6. Amazon RDS (Relational Database Service)**
- **Maximum DB Instances per Region**: 40 (for each database engine)
- **Maximum RDS DB Storage Size**: 64 TB (for MySQL, MariaDB, PostgreSQL, and Oracle)
- **Maximum RDS DB Storage Size for SQL Server**: 16 TB
- **Maximum DB Snapshots per Region**: 1,000
- **Maximum DB Subnet Groups per Region**: 20
- **Maximum Read Replicas per DB Instance**: 5 (for MySQL, PostgreSQL)
- **Maximum DB Connections (RDS for MySQL)**: 16,000

---

### **7. AWS Lambda**
- **Maximum Concurrent Executions per Account**: 1,000 (by default)
- **Maximum Lambda Function Timeout**: 15 minutes
- **Maximum Package Size for Lambda Function (Zipped)**: 50 MB
- **Maximum Package Size for Lambda Function (Unzipped)**: 250 MB
- **Maximum Memory Allocation per Function**: 10 GB

---

### **8. Amazon CloudWatch**
- **Maximum Alarms per Region**: 1,000
- **Maximum Custom Metrics per Region**: 150,000
- **Maximum Log Groups per Region**: 10,000
- **Maximum Log Streams per Log Group**: 50,000

---

### **9. AWS CloudFormation**
- **Maximum Stacks per Region**: 200
- **Maximum Resources per Stack**: 500
- **Maximum Template Size**: 51.2 KB (for direct upload)
- **Maximum Template Size (via S3 URL)**: 460.8 KB

---

### **10. IAM (Identity and Access Management)**
- **Maximum Users per Account**: 5,000
- **Maximum Groups per Account**: 300
- **Maximum Groups per User**: 10
- **Maximum Inline Policies per User/Role**: 1
- **Maximum Policies per Role**: 10
- **Maximum Groups per Policy**: 5

---

### **11. Amazon EBS (Elastic Block Store)**
- **Maximum Volumes per Region**: 1,000
- **Maximum Volume Size**: 16 TB
- **Maximum IOPS for EBS Volumes**: 64,000 (Provisioned IOPS SSD)
- **Maximum Throughput per EBS Volume**: 1,000 MB/s (for io2 volumes)
- **Maximum Snapshots per Region**: 100,000

---

### **12. Amazon SNS (Simple Notification Service)**
- **Maximum Topics per Account**: 100,000
- **Maximum Subscriptions per Topic**: 12,500
- **Maximum Message Size**: 256 KB

---

### **13. Amazon SQS (Simple Queue Service)**
- **Maximum Queue Length**: Unlimited
- **Maximum Message Size**: 256 KB
- **Maximum Retention Period**: 14 days

---

### **14. AWS Elastic Beanstalk**
- **Maximum Applications per Region**: 50
- **Maximum Environments per Application**: 10
- **Maximum Application Versions per Application**: 100
- **Maximum EC2 Instances per Environment**: 50

---

### **15. AWS CloudTrail**
- **Maximum Trails per Region**: 5
- **Maximum CloudTrail Events per Second**: 500

---

### **16. Amazon Elastic File System (EFS)**
- **Maximum File Systems per Account**: 100
- **Maximum File System Size**: Unlimited
- **Maximum Throughput**: 10 GB/s per file system
- **Maximum Number of Files per File System**: Unlimited

---

### **17. AWS Direct Connect**
- **Maximum Virtual Interfaces per Direct Connect Connection**: 50
- **Maximum Virtual Connections per Account**: 50

---

### **18. AWS Key Management Service (KMS)**
- **Maximum Keys per Region**: 100,000
- **Maximum Aliases per Key**: 20
- **Maximum Grants per Key**: 1,000

---

### **19. AWS Elasticache**
- **Maximum Clusters per Region**: 500
- **Maximum Nodes per Cluster**: 50 (for Redis)
- **Maximum Nodes per Cache Cluster**: 20 (for Memcached)

---

### **20. AWS Aurora**
- **Maximum Aurora DB Cluster Volume Size**: 128 TB
- **Maximum Aurora Replicas**: 15 (for MySQL-compatible Aurora)
- **Maximum Storage per Aurora Instance**: 64 TB

---

### **21. AWS CloudFront**
- **Maximum Distributions per AWS Account**: 200
- **Maximum Cache Behaviors per Distribution**: 50
- **Maximum HTTP Request/Response Headers per Distribution**: 100

---

### **22. Elastic IP (EIP)**
- **Maximum EIPs per Region**: 5 (can request more)

---

### **23. AWS WAF (Web Application Firewall)**
- **Maximum Web ACLs per Account**: 100
- **Maximum Rules per Web ACL**: 150
- **Maximum Web ACLs per Region**: 100

---
