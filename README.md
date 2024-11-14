# **DevOps Tools and Cloud Platforms – Service Limits, Quotas, and Best Practices**

This document provides an overview of common **service limits** and **quotas** for major cloud platforms (AWS, Azure) and other DevOps tools (Terraform, Docker, Kubernetes, Git, Jenkins, etc.), as well as some **basic troubleshooting scenarios** and **best practices**.

---

## **AWS Service Limits and Quotas**

### **1. EC2 (Elastic Compute Cloud)**
- **VMs per Region**: 20 (default)
- **Maximum EBS Volumes**: 1,000 per Region
- **Maximum Instance Types per Region**: Varies (request increase)

### **2. VPC (Virtual Private Cloud)**
- **Maximum VPCs per Region**: 5
- **Subnets per VPC**: 200
- **Security Groups per VPC**: 500

### **3. S3 (Simple Storage Service)**
- **Object Size**: 5 TB
- **Bucket Name Length**: 63 characters
- **Maximum Buckets per Account**: 100

### **4. IAM (Identity and Access Management)**
- **Users per Account**: 5,000
- **Groups per Account**: 300
- **Roles per Account**: 1,000

### **5. RDS (Relational Database Service)**
- **DB Instances per Region**: 40
- **Maximum DB Storage**: 64 TB

### **6. SQS (Simple Queue Service)**
- **Message Size**: 256 KB
- **Maximum Queue Length**: Unlimited

### **7. Lambda**
- **Max Concurrent Executions**: 1,000
- **Memory per Function**: 10 GB
- **Execution Timeout**: 15 minutes

---

## **Azure Service Limits and Quotas**

### **1. Virtual Machines (VM)**
- **VMs per Subscription**: 20,000 (default)
- **Maximum Disk Size**: 4 TB
- **Maximum VM Network Interfaces**: 8 per VM

### **2. Virtual Network (VNet)**
- **VNets per Region**: 50
- **Maximum Subnets per VNet**: 1,000
- **Maximum Public IPs per Subscription**: 1,000

### **3. Storage Accounts**
- **Maximum Storage Accounts per Subscription**: 200
- **Maximum Blob Size**: 5 TB
- **Maximum Containers per Account**: 500,000

### **4. Kubernetes Service (AKS)**
- **Nodes per Cluster**: 5,000
- **Pods per Cluster**: 150,000
- **Node Pools per Cluster**: 100

### **5. Azure SQL Database**
- **Databases per Server**: 500
- **Maximum Storage Size**: 16 TB
- **DTUs per Database**: 4,000

### **6. App Service (Web Apps)**
- **Web Apps per Subscription**: 200
- **Max Instances per Plan**: 20,000

---

## **DevOps Tools**

### **1. Terraform**
- **Loops**: `count`, `for_each`
- **Conditional Logic**: Use `count` and `if` statements
- **Local Exec Provisioner**: `local-exec` for running commands
- **Example**: Using `count` to create multiple resources
  ```hcl
  resource "aws_s3_bucket" "buckets" {
    count = 5
    bucket = "my-bucket-${count.index}"
  }
  ```

### **2. Docker**
- **CMD vs ENTRYPOINT**: 
  - **CMD**: Provides default arguments for the container. Can be overridden at runtime.
  - **ENTRYPOINT**: Defines the default executable. Cannot be overridden.
  - **Example**:
    ```dockerfile
    # CMD example
    CMD ["python", "app.py"]
    # ENTRYPOINT example
    ENTRYPOINT ["python"]
    ```

### **3. Kubernetes**
- **ConfigMaps**: Store configuration data in key-value pairs.
- **Pods**: The smallest deployable unit.
- **Services**: Expose pods to access from outside the cluster.

### **4. Git**
- **Fetch vs Pull**: 
  - **Fetch**: Downloads changes but does not merge.
  - **Pull**: Fetches changes and merges them into the local branch.
- **Cherry-pick**: Apply a commit from another branch.
  ```bash
  git cherry-pick <commit-hash>
  ```
- **Amend**: Modify the last commit.
  ```bash
  git commit --amend
  ```

---

## **Scenario-Based Troubleshooting**

### **1. AWS EC2 Instance Unreachable**
- **Issue**: EC2 instance cannot be reached.
- **Fix**:
  - Check **Security Group** rules.
  - Ensure **NACL** is not blocking the traffic.
  - Verify the **Route Table** configurations.

### **2. Azure VM Not Starting**
- **Issue**: VM stuck in a "starting" state.
- **Fix**:
  - Check the **boot diagnostics** for error logs.
  - Ensure there are no issues with **VM Size** availability.
  - Verify that the **Storage Account** used by the VM is not full.

---

## **Best Practices**

### **1. Security**
- Use **MFA** and **IAM Roles** for secure access.
- **Encrypt** sensitive data in services like S3, RDS, and Azure Storage.

### **2. Backup and Disaster Recovery**
- **Cross-region replication** for AWS and Azure to ensure disaster recovery.
- Regularly test backup and restore procedures.

### **3. Scaling**
- Use **Auto Scaling** for EC2, Azure VM Scale Sets, and Kubernetes to handle varying loads.

---
