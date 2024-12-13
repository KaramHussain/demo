## VPC MODULE

The Terraform VPC Module is a highly reusable, configurable, and professional solution for creating a Virtual Private Cloud (VPC) in AWS. This module simplifies the process of provisioning a secure and scalable networking layer by automating the creation of:

- A VPC with DNS support
- Public and private subnets
- Internet Gateway (IGW)
- NAT Gateway (optional)
- Route Tables and their associations
- DHCP Options
- Network ACLs (NACLs)

With clear configuration options and descriptive tags, this module is designed to suit development, testing, and production environments.

---

## Why Use This Module?

- **Simplicity**: Provides a one-stop solution for creating a robust network architecture.
- **Reusability**: Easily adaptable for different environments and projects.
- **Flexibility**: Configurable attributes for CIDR blocks, subnets, and NAT Gateways.
- **Scalability**: Supports multiple subnets across availability zones.
- **Best Practices**: Implements AWS best practices for VPC design.

---

## Features

- **VPC Configuration**
  - Supports custom CIDR blocks.
  - Enables DNS resolution and hostnames.
- **Subnet Configuration**
  - Configurable public and private subnets.
  - Spread across multiple availability zones.
- **Routing Configuration**
  - Internet Gateway for public access.
  - NAT Gateway for private subnet internet access.
- **Optional DHCP Options**
  - Custom domain name and DNS servers.
- **Network ACLs**
  - Fine-grained access control for public and private subnets.
- **Tagging**
  - Customizable tags for easy resource identification.

---

## Inputs

Below is a detailed table of all input variables supported by the module:

| **Variable Name**          | **Description**                                            | **Type**       | **Default**               | **Required** |
|----------------------------|------------------------------------------------------------|----------------|---------------------------|--------------|
| `vpc_name`                 | Name of the VPC.                                           | `string`       | "my-vpc"                | Yes          |
| `vpc_cidr`                 | CIDR block for the VPC.                                    | `string`       | "10.0.0.0/16"           | Yes          |
| `public_subnets_cidrs`     | List of CIDR blocks for public subnets.                    | `list(string)` | `[]`                      | Yes          |
| `private_subnets_cidrs`    | List of CIDR blocks for private subnets.                   | `list(string)` | `[]`                      | Yes          |
| `azs`                      | List of availability zones for subnets.                   | `list(string)` | `[]`                      | Yes          |
| `environment`              | Tag indicating the environment (e.g., dev, prod).          | `string`       | "dev"                   | Yes          |
| `create_nat_gateway`       | Whether to create a NAT Gateway.                          | `bool`         | `true`                   | No           |
| `dhcp_domain_name`         | Domain name for DHCP options.                             | `string`       | "example.com"           | No           |
| `dhcp_domain_name_servers` | Domain name servers for DHCP options.                     | `list(string)` | `["AmazonProvidedDNS"]` | No           |

---

## Outputs

The module provides the following outputs:

| **Output Name**            | **Description**                                            |
|----------------------------|------------------------------------------------------------|
| `vpc_id`                   | ID of the VPC.                                             |
| `public_subnets_ids`       | List of IDs of the public subnets.                         |
| `private_subnets_ids`      | List of IDs of the private subnets.                        |
| `internet_gateway_id`      | ID of the Internet Gateway.                                |
| `nat_gateway_id`           | ID of the NAT Gateway (if created).                       |
| `route_table_public_id`    | ID of the public route table.                              |
| `route_table_private_id`   | ID of the private route table.                             |

---

## Usage

Below is an example of how to use the module in a Terraform configuration:

```hcl
module "demo_vpc" {
  source = "./vpc_module" # Replace with the path to your VPC module

  # VPC Configuration
  vpc_name = "demo_VPC"
  vpc_cidr = "10.1.0.0/16"

  # Public Subnet Configuration
  public_subnets_cidrs = ["10.1.1.0/24", "10.1.2.0/24"]

  # Private Subnet Configuration
  private_subnets_cidrs = ["10.1.3.0/24", "10.1.4.0/24"]

  # Availability Zones
  azs = ["us-west-2a", "us-west-2b"]

  # Environment Tag
  environment = "demo"

  # NAT Gateway
  create_nat_gateway = true

  # DHCP Options
  dhcp_domain_name = "demo.local"
  dhcp_domain_name_servers = ["AmazonProvidedDNS"]
}
```

---
## Best Practices

- **Environment Segregation**: Use different VPCs for dev, staging, and prod environments.
- **CIDR Planning**: Ensure CIDR blocks do not overlap with existing networks.
- **Tagging**: Use descriptive tags to identify resources easily.
- **Cost Management**: Disable NAT Gateway in non-production environments to save costs.

---

## Troubleshooting

- **Subnet CIDR Overlap**: Ensure public and private subnet CIDR blocks do not overlap.
- **Availability Zones**: Verify that the specified availability zones are available in your region.
- **Permission Issues**: Ensure your IAM role has sufficient permissions to create VPC resources.
# S3 Bucket Module Usage Guide

This Terraform module allows you to create an S3 bucket with various configurations such as website hosting, versioning, CORS rules, lifecycle policies, and more.

## Features

| Feature                    | Description                                              |
|----------------------------|----------------------------------------------------------|
| Bucket Creation             | Creates an S3 bucket with the provided name or prefix    |
| Object Locking              | Enables or disables object locking for the bucket        |
| Website Hosting             | Configures static website hosting with index and error pages |
| Versioning                  | Enables or disables versioning on the bucket             |
| CORS Configuration          | Configures Cross-Origin Resource Sharing (CORS) rules    |
| Lifecycle Policies          | Manages object lifecycle with expiration policies       |
| Public Access Block         | Configures public access blocking settings               |
| Bucket Policy               | Attaches a custom policy to the S3 bucket               |

## Input Variables

| Variable                    | Description                                              | Type               | Default Value |
|-----------------------------|----------------------------------------------------------|--------------------|---------------|
| `create_bucket`              | Flag to create the S3 bucket                             | `bool`             | `true`        |
| `bucket`                     | Name of the S3 bucket                                    | `string`           | `null`        |
| `bucket_prefix`              | Prefix for the S3 bucket name                            | `string`           | `null`        |
| `object_lock_enabled`        | Flag to enable object locking                            | `bool`             | `false`       |
| `tags`                       | Tags for the S3 bucket                                   | `map(string)`      | `{}`          |
| `website`                    | Website configuration for the S3 bucket                 | `object`           | `{}`          |
| `versioning`                 | Versioning configuration for the S3 bucket              | `object`           | `{}`          |
| `cors_rule`                  | CORS rules for the S3 bucket                             | `any`              | `[]`          |
| `lifecycle_rule`             | Lifecycle rules for the S3 bucket                        | `any`              | `[]`          |
| `block_public_access`        | Block public access settings                             | `object`           | `{}`          |
| `attach_policy`              | Flag to attach a policy to the S3 bucket                 | `bool`             | `false`       |
| `policy`                     | Policy to attach to the S3 bucket (in JSON format)       | `object`           | `null`        |

## How to Use

1. Clone or copy this module to your Terraform project.
2. In your `main.tf`, define the module and set the required variables, such as `bucket` and `create_bucket`.

Example:

```hcl
module "s3_bucket" {
  source = "./modules/s3"  # Path to the S3 module
  
  # Flag to create the S3 bucket
  create_bucket = true  # Set this to false to skip bucket creation

  # S3 bucket name
  bucket = var.demobucket  # This will be the name of the S3 bucket you want to create
  
  # Enable object locking for the bucket
  object_lock_enabled = true  # Set to true to enable object lock, false to disable

  # Tags for the S3 bucket
  tags = { 
    Environment = "production"  # Add your environment tag
    Project     = "my-cloud-project"  # Add your project tag
  }

  # Website configuration for static website hosting
  website = { 
    index_document = "index.html"  # Set your index document name
    error_document = "error.html"  # Set your error document name
  }

  # Versioning configuration to enable or suspend versioning
  versioning = { 
    enabled    = true  # Set to true to enable versioning, false to disable
    mfa_delete = false  # Set to true to enable MFA delete
  }

  # CORS rules configuration to control cross-origin access
  cors_rule = jsonencode([ 
    { 
      allowed_methods = ["GET", "POST"]  # Set allowed methods
      allowed_origins = ["https://www.example.com"]  # Set allowed origins
      allowed_headers = ["*"]  # Set allowed headers
      expose_headers  = ["x-amz-server-side-encryption"]  # Set expose headers
      max_age_seconds = 3000  # Set max age for CORS preflight requests
    } 
  ])

  # Lifecycle rules for managing object lifecycle
  lifecycle_rule = jsonencode([ 
    { 
      id      = "expire-old-objects"  # Rule ID
      enabled = true  # Enable the rule
      expiration = { 
        days = 30  # Set expiration days for objects
      }
    } 
  ])

  # Flag to attach a policy to the bucket
  attach_policy = true  # Set to true to attach a policy to the bucket

  # Block public access configuration
  block_public_access = { 
    block_public_acls       = false  # Set to true to block public ACLs
    block_public_policy     = false  # Set to true to block public policy
    ignore_public_acls      = false  # Set to true to ignore public ACLs
    restrict_public_buckets = false  # Set to true to restrict public buckets
  }

  # Policy to attach to the bucket (example policy in JSON format)
  policy = { 
    JSON = jsonencode({ 
      Version = "2012-10-17", 
      Statement = [ 
        { 
          Sid       = "PublicReadGetObject", 
          Effect    = "Allow", 
          Principal = "*", 
          Action    = "s3:GetObject", 
          Resource  = "arn:aws:s3:::${var.demobucket}/*" 
        } 
      ] 
    }) 
  }
}
```