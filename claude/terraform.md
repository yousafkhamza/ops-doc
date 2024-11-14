# Terraform Interview Questions and Answers

## Basic Concepts

1. **Q: What is Terraform and how does it work?**
   A: Terraform is an Infrastructure as Code (IaC) tool that:
   - Declares infrastructure in HCL (HashiCorp Configuration Language)
   - Manages state of infrastructure
   - Provides resource dependency management
   - Supports multiple cloud providers

2. **Q: Explain Terraform workflow**
   A: Main commands:
   ```bash
   terraform init      # Initialize working directory
   terraform plan     # Preview changes
   terraform apply    # Apply changes
   terraform destroy  # Destroy infrastructure
   ```

## Configuration Basics

1. **Q: Basic Terraform configuration structure?**
   A: Example:
   ```hcl
   # Provider configuration
   provider "aws" {
     region = "us-west-2"
   }

   # Resource definition
   resource "aws_instance" "example" {
     ami           = "ami-0c55b159cbfafe1f0"
     instance_type = "t2.micro"
     
     tags = {
       Name = "example-instance"
     }
   }
   ```

2. **Q: How to use variables?**
   A: Variable definitions:
   ```hcl
   # variables.tf
   variable "instance_type" {
     description = "EC2 instance type"
     type        = string
     default     = "t2.micro"
   }

   # Usage in main.tf
   resource "aws_instance" "example" {
     instance_type = var.instance_type
   }
   ```

## State Management

1. **Q: Explain Terraform state and its importance**
   A: State:
   - Tracks resource metadata
   - Maps real resources to configuration
   - Tracks dependencies
   Example remote state:
   ```hcl
   terraform {
     backend "s3" {
       bucket = "terraform-state"
       key    = "prod/terraform.tfstate"
       region = "us-west-2"
     }
   }
   ```

2. **Q: How to import existing resources?**
   A: Import process:
   ```bash
   # Add resource to configuration
   resource "aws_instance" "example" {
     # ... configuration ...
   }

   # Import command
   terraform import aws_instance.example i-1234567890abcdef0
   ```

## Loops and Iteration

1. **Q: Explain different types of loops in Terraform**
   A: Types:
   
   - Count (numeric loop):
   ```hcl
   resource "aws_instance" "server" {
     count = 3
     ami   = "ami-xyz"
     tags  = {
       Name = "server-${count.index}"
     }
   }
   ```

   - For_each (map/set loop):
   ```hcl
   resource "aws_instance" "server" {
     for_each = toset(["web", "app", "db"])
     ami      = "ami-xyz"
     tags     = {
       Name = "server-${each.key}"
     }
   }
   ```

   - Dynamic blocks:
   ```hcl
   resource "aws_security_group" "example" {
     dynamic "ingress" {
       for_each = var.service_ports
       content {
         from_port   = ingress.value
         to_port     = ingress.value
         protocol    = "tcp"
         cidr_blocks = ["0.0.0.0/0"]
       }
     }
   }
   ```

## Conditionals

1. **Q: How to implement conditional resource creation?**
   A: Examples:

   - Count with condition:
   ```hcl
   resource "aws_instance" "dev" {
     count = var.environment == "dev" ? 1 : 0
     ami   = "ami-xyz"
   }
   ```

   - Conditional expressions:
   ```hcl
   resource "aws_instance" "example" {
     instance_type = var.environment == "prod" ? "t2.medium" : "t2.micro"
   }
   ```

## Functions and Expressions

1. **Q: Common Terraform functions?**
   A: Examples:
   ```hcl
   # String manipulation
   local {
     upper_name = upper(var.name)
     lower_name = lower(var.name)
     title_name = title(var.name)
   }

   # List/Map functions
   locals {
     merged_map = merge(var.map1, var.map2)
     flattened_list = flatten(var.nested_list)
     list_contains = contains(var.list, "value")
   }
   ```

## Modules

1. **Q: How to create and use modules?**
   A: Module structure:
   ```hcl
   # modules/vpc/main.tf
   resource "aws_vpc" "main" {
     cidr_block = var.vpc_cidr
   }

   # Root main.tf
   module "vpc" {
     source = "./modules/vpc"
     vpc_cidr = "10.0.0.0/16"
   }
   ```

## Provisioners and Null Resources

1. **Q: How to use provisioners?**
   A: Examples:
   ```hcl
   resource "aws_instance" "example" {
     # ... instance configuration ...

     provisioner "remote-exec" {
       inline = [
         "sudo apt-get update",
         "sudo apt-get install -y nginx"
       ]
     }
   }

   # Null resource for arbitrary actions
   resource "null_resource" "example" {
     triggers = {
       cluster_instance_ids = join(",", aws_instance.cluster[*].id)
     }

     provisioner "local-exec" {
       command = "echo ${self.triggers.cluster_instance_ids} > instance_ids.txt"
     }
   }
   ```

## Workspace Management

1. **Q: How to use Terraform workspaces?**
   A: Workspace commands:
   ```bash
   # Create new workspace
   terraform workspace new dev

   # List workspaces
   terraform workspace list

   # Select workspace
   terraform workspace select prod

   # Usage in configuration
   resource "aws_instance" "example" {
     instance_type = terraform.workspace == "prod" ? "t2.medium" : "t2.micro"
   }
   ```

## Common Issues and Solutions

1. **Q: State lock issues**
   A: Solutions:
   ```bash
   # Force unlock state
   terraform force-unlock LOCK_ID

   # Check who holds lock
   terraform state list
   ```

2. **Q: Dependency issues**
   A: Use depends_on:
   ```hcl
   resource "aws_instance" "app" {
     depends_on = [aws_db_instance.database]
     # ... configuration ...
   }
   ```

3. **Q: State refresh issues**
   A: Solutions:
   ```bash
   # Refresh state
   terraform refresh

   # Update state manually
   terraform state rm aws_instance.example
   terraform import aws_instance.example i-1234567890abcdef0
   ```

## Advanced Topics

1. **Q: Data Source usage**
   A: Example:
   ```hcl
   data "aws_ami" "ubuntu" {
     most_recent = true
     filter {
       name   = "name"
       values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
     }
   }

   resource "aws_instance" "web" {
     ami = data.aws_ami.ubuntu.id
   }
   ```

2. **Q: Provider configuration with aliases**
   A: Example:
   ```hcl
   provider "aws" {
     alias  = "us-east-1"
     region = "us-east-1"
   }

   provider "aws" {
     alias  = "us-west-2"
     region = "us-west-2"
   }

   resource "aws_instance" "east" {
     provider = aws.us-east-1
     # ... configuration ...
   }
   ```

## Best Practices

1. **Q: State management best practices?**
   A: Recommendations:
   - Use remote state
   - Enable state locking
   - Use state workspaces
   - Regular state backup
   - Use state encryption

2. **Q: Code organization?**
   A: Structure:
   ```
   project/
   ├── main.tf
   ├── variables.tf
   ├── outputs.tf
   ├── terraform.tfvars
   ├── modules/
   │   ├── vpc/
   │   ├── ec2/
   │   └── rds/
   └── environments/
       ├── dev/
       └── prod/
   ```

## Testing and Validation

1. **Q: How to validate Terraform code?**
   A: Tools and commands:
   ```bash
   # Format code
   terraform fmt

   # Validate configuration
   terraform validate

   # Tflint for extended validation
   tflint

   # Use pre-commit hooks
   pre-commit install
   ```

2. **Q: Testing infrastructure code?**
   A: Tools:
   - Terratest for integration testing
   - Kitchen-Terraform for testing
   - Checkov for security scanning