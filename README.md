# 1. VPC MODULE Usage Guide

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



# 2. S3 Bucket Module Usage Guide

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

# 3. IAM Role Module for EC2 Instances

This Terraform module creates an IAM role, an associated IAM policy, and an instance profile to be attached to EC2 instances. The role allows EC2 instances to assume a specific policy defined by the user.

## How to Use

- **Step 1:** Define the module in your Terraform configuration.
- **Step 2:** Provide the required variables such as `role_name`, `policy_document`, and `tags`.
- **Step 3:** Apply the Terraform configuration.

### Example Usage

```hcl
module "ec2_iam_role" {
  source          = "./modules/Role"
  role_name       = "demoEc2InstanceRole"
  policy_document = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action   = ["s3:ListBucket", "s3:GetObject"]
        Effect   = "Deny"
        Resource = ["arn:aws:s3:::*"]
      }
    ]
  })
  tags = {
    Environment = "dev"
    Project     = "demo"
  }
}
```
## Inputs

| Name             | Description                       | Type         | Default | Required |
|------------------|-----------------------------------|--------------|---------|----------|
| `role_name`      | The name of the IAM role          | `string`     | n/a     | yes      |
| `policy_document`| The IAM policy document for the role | `string`  | n/a     | yes      |
| `tags`           | Tags for the IAM role             | `map(string)`| `{}`    | no       |

## Outputs

| Name                      | Description                                               |
|---------------------------|-----------------------------------------------------------|
| `instance_profile_arn`   | The ARN of the IAM instance profile                       |
| `iam_instance_profile_name` | The name of the IAM Instance Profile to attach to the EC2 instance |

## How to Create the IAM Role

### `main.tf`

```hcl
# Module to create the IAM role and instance profile
module "ec2_iam_role" {
  source          = "./modules/Role"
  role_name       = "demoEc2InstanceRole"
  policy_document = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action   = ["s3:ListBucket", "s3:GetObject"]
        Effect   = "Deny"
        Resource = ["arn:aws:s3:::*"]
      }
    ]
  })
  tags = {
    Environment = "dev"
    Project     = "demo"
  }
}
```

## Notes

- Ensure your AWS credentials are properly configured.
- The IAM role assumes the `ec2.amazonaws.com` service.
- Customize the `policy_document` to fit your use case.





# 4. Terraform Security Group Module

This module creates an AWS Security Group with customizable ingress rules, a VPC, and tags. The security group allows you to define a set of ingress rules and applies an egress rule to allow all outbound traffic.

## Inputs

| Name             | Description                                                      | Type                                                   | Default | Required |
|------------------|------------------------------------------------------------------|--------------------------------------------------------|---------|----------|
| `name`           | The name of the security group.                                  | `string`                                               | n/a     | Yes      |
| `description`    | The description of the security group.                           | `string`                                               | n/a     | Yes      |
| `vpc_id`         | The VPC ID where the security group will be created.             | `string`                                               | n/a     | Yes      |
| `ingress_rules`  | A list of ingress rules to be added to the security group.       | `list(object({cidr_ip = string, description = string, port = number, protocol = string}))` | `[]`     | Yes      |
| `tags`           | Tags to apply to the security group.                              | `map(string)`                                          | `{}`    | Yes      |

## Outputs

| Name                | Description                                           |
|---------------------|-------------------------------------------------------|
| `security_group_id` | The ID of the created security group.                 |

## Behavior

- Creates an AWS Security Group within the specified VPC.
- Allows customizable ingress rules based on provided CIDR blocks, ports, and protocols.
- Always applies an egress rule that allows all outbound traffic.
- Outputs the Security Group ID for reference in other resources.

## Usage Example

You can use this module in your Terraform configuration as shown below. 

### Step 1: Define Your Security Group Module

```hcl
module "web_security_group" {
  source      = "./modules/securitygroup"
  vpc_id      = module.demo_vpc.vpc_id
  name        = "web-sg"
  description = "Security group for web servers"

  ingress_rules = [
    {
      cidr_ip     = "0.0.0.0/0"
      description = "Allow HTTP"
      port        = 80
      protocol    = "tcp"
    },
    {
      cidr_ip     = "0.0.0.0/0"
      description = "Allow SSH"
      port        = 22
      protocol    = "tcp"
    }
  ]

  tags = {
    Environment = "dev"
    Project     = "demo"
  }
}

```
## Notes

- The **ingress rules** should be specified as a list of objects, each defining the following properties:
  - `cidr_ip`: The CIDR block to allow traffic from (e.g., `0.0.0.0/0` for all IP addresses).
  - `description`: A description of the rule (e.g., "Allow HTTP").
  - `port`: The port or range of ports to allow (e.g., `80` for HTTP).
  - `protocol`: The network protocol (e.g., `tcp`).
  
- This module automatically creates an **egress rule** that allows all outbound traffic.

- The **security group ID** is outputted as `security_group_id`, which you can use in other resources, such as EC2 instances.




# 5. EC2 Instance Module Usage Guide
# Terraform EC2 Instance Module

This module creates an EC2 instance with customizable configurations for the AMI, instance type, security groups, EBS volume, and optional Elastic IP allocation.

## Inputs

| Name                    | Description                                                           | Type         | Default       | Required |
|-------------------------|-----------------------------------------------------------------------|--------------|---------------|----------|
| `ami`                    | AMI ID for the EC2 instance                                           | `string`     | n/a           | Yes      |
| `subnet_id`              | Subnet ID in which the EC2 instance will be created                   | `string`     | n/a           | Yes      |
| `instance_type`          | EC2 instance type (e.g., `t2.micro`, `t3.medium`)                     | `string`     | n/a           | Yes      |
| `security_group_ids`     | List of security group IDs to associate with the EC2 instance         | `list(string)` | n/a         | Yes      |
| `instance_profile`       | IAM instance profile name                                             | `string`     | n/a           | Yes      |
| `tags`                   | Tags to apply to the EC2 instance                                      | `map(string)`| `{}`          | Yes      |
| `ebs_volume_size`        | Size of the EBS volume in GiB (default: 20 GiB)                       | `number`     | `20`          | No       |
| `ebs_volume_type`        | Type of the EBS volume (e.g., `gp2`, `gp3`, `io1`, etc.)              | `string`     | `gp2`         | No       |
| `enable_elastic_ip`      | Whether to allocate an Elastic IP to the EC2 instance (`true` or `false`) | `bool`     | `false`       | No       |
| `delete_on_termination`  | Whether to delete the Elastic IP when the instance is terminated      | `bool`       | `false`       | No       |

## Outputs

| Name               | Description                                                       |
|--------------------|-------------------------------------------------------------------|
| `instance_id`      | The ID of the created EC2 instance                                |
| `public_ip`        | The public IP address of the EC2 instance (Elastic IP if allocated) |
| `elastic_ip`       | The Elastic IP if allocated, `null` if Elastic IP is not enabled  |

## Behavior

- Creates an EC2 instance with customizable settings such as AMI ID, instance type, and IAM profile.
- Attaches security groups to the EC2 instance.
- Configures an EBS volume with the specified size and type.
- Optionally allocates an Elastic IP if the `enable_elastic_ip` input is set to `true`.
- Provides outputs such as the EC2 instance ID, public IP, and Elastic IP (if applicable).

## Usage Example

Below is an example of how to use this EC2 module in your Terraform configuration.

### Step 1: Define Your EC2 Module

```hcl
module "web_server" {
  source            = "./modules/EC2"
  ami               = "ami-0453ec754f44f9a4a"  ## Update this according to the region and AMI you want to choose
  instance_type     = "t2.micro"
  subnet_id         = module.demo_vpc.public_subnets_ids[0]
  security_group_ids = [module.web_security_group.security_group_id]
  instance_profile  = module.ec2_iam_role.iam_instance_profile_name
  ebs_volume_size   = 24
  ebs_volume_type   = "gp3"
  enable_elastic_ip = true
  delete_on_termination = true
  tags = {
    Name        = "demoServer"
    # Environment = "dev"
  }
}
```

## Notes

- Make sure to **update the `ami` variable** with the correct AMI ID for your region.

- The **`enable_elastic_ip`** flag will determine whether an Elastic IP is created and associated with the EC2 instance.

- The **instance profile**, **security groups**, and **subnet ID** are required to ensure the EC2 instance has appropriate access and is placed in the correct network and these 3 are the outputs of previous resources which we have created.



# 6. CloudFront Terraform Module Usage Guide

This module allows you to create an AWS CloudFront distribution with various configurable features. It simplifies the deployment of CloudFront distributions and provides flexibility for customizing origins, cache behaviors, and SSL/TLS settings.

## Features
- Create and manage AWS CloudFront distributions.
- Supports both S3 and custom origins (e.g., HTTP/HTTPS servers or load balancers).
- Enable IPv6 support for the CloudFront distribution.
- Customizable cache behaviors with path-based rules.
- Ability to configure secure access for S3 origins using Origin Access Identity (OAI).
- Optionally use a custom SSL certificate from AWS ACM.
- Configure viewer protocols and HTTP methods.
- Detailed configuration options for cache compression and query string handling.

## Inputs

| **Variable Name**               | **Type**           | **Description**                                                                                               | **Default**           |
|----------------------------------|--------------------|---------------------------------------------------------------------------------------------------------------|-----------------------|
| `comment`                        | `string`           | A descriptive comment for the CloudFront distribution.                                                         | `"My awesome CloudFront"` |
| `enabled`                        | `bool`             | Whether the CloudFront distribution is enabled.                                                                | `true`                |
| `is_ipv6_enabled`                | `bool`             | Enable IPv6 support for CloudFront.                                                                            | `true`                |
| `price_class`                    | `string`           | Pricing class for the CloudFront distribution (e.g., `PriceClass_All`).                                        | `"PriceClass_All"`    |
| `retain_on_delete`               | `bool`             | Whether to retain resources when the distribution is deleted.                                                  | `false`               |
| `wait_for_deployment`            | `bool`             | Whether to wait for deployment to complete before returning.                                                   | `false`               |
| `create_origin_access_identity`  | `bool`             | Whether to create an Origin Access Identity for secure access to S3 buckets.                                   | `true`                |
| `origin_access_identities`       | `map(string)`      | A map to define access identities for S3 buckets CloudFront can access.                                        | `{}`                  |
| `origin`                         | `map(object)`      | A map to define origins for CloudFront (e.g., S3, HTTP server).                                                | `{}`                  |
| `default_cache_behavior`         | `map(object)`      | Default cache behavior settings for the CloudFront distribution.                                               | `{}`                  |
| `ordered_cache_behavior`         | `list(object)`     | Ordered cache behaviors for specific path patterns in CloudFront.                                              | `[]`                  |
| `aliases`                        | `list(string)`     | List of alternate domain names (CNAMEs) for CloudFront (optional).                                            | `[]`                  |
| `viewer_certificate`             | `map(object)`      | Configuration for the viewer SSL certificate (optional).                                                      | `{}`                  |

## Flexibility Offered

- **Multiple Origins:** Support for both S3 and custom HTTP origins.
- **Custom Cache Behaviors:** Configure cache rules for specific path patterns (e.g., `/static/*`).
- **Viewer Protocol Policy:** You can choose between `allow-all`, `redirect-to-https`, and `https-only` for viewer requests.
- **Custom SSL Certificates:** Optionally use a custom SSL certificate for secure connections.
- **Origin Access Identity:** Secure access to S3 buckets via Origin Access Identity, preventing direct access to the bucket.

## Variables Structure

### `origin`

The `origin` variable allows you to define various origin configurations. Below is an example structure:

```hcl
origin = {
  something = {
    domain_name = "something.example.com"
    custom_origin_config = {
      http_port              = 80
      https_port             = 443
      origin_protocol_policy = "match-viewer"
      origin_ssl_protocols   = ["TLSv1"]
    }
  }
  s3_one = {
    domain_name = "${var.demobucket}.s3.amazonaws.com"
    s3_origin_config = {
      origin_access_identity = "s3_bucket_one"
    }
  }
}
```

## default_cache_behavior
This structure defines how CloudFront should handle caching for the default path:
```hcl
default_cache_behavior = {
  target_origin_id       = "something"
  viewer_protocol_policy = "allow-all"
  allowed_methods        = ["GET", "HEAD", "OPTIONS"]
  cached_methods         = ["GET", "HEAD"]
  compress               = true
  query_string           = true
}
```

## ordered_cache_behavior
You can define cache behaviors for specific URL path patterns:
```hcl
ordered_cache_behavior = [
  {
    path_pattern           = "/static/*"
    target_origin_id       = "s3_one"
    viewer_protocol_policy = "redirect-to-https"
    allowed_methods        = ["GET", "HEAD", "OPTIONS"]
    cached_methods         = ["GET", "HEAD"]
    compress               = true
    query_string           = true
  }
]
```


## Example Usage:
To use this module, reference it in your Terraform configuration like so:
```hcl
module "cloudfront" {
  source = "./modules/cloudfront"

  comment             = "My CloudFront"
  enabled             = true
  is_ipv6_enabled     = true
  price_class         = "PriceClass_All"
  retain_on_delete    = false
  wait_for_deployment = false

  create_origin_access_identity = true
  origin_access_identities = {
    s3_bucket_one = "My CloudFront can access"
  }

  origin = {
    something = {
      domain_name = "something.example.com"
      custom_origin_config = {
        http_port              = 80
        https_port             = 443
        origin_protocol_policy = "match-viewer"
        origin_ssl_protocols   = ["TLSv1"]
      }
    }
    s3_one = {
      domain_name = "${var.demobucket}.s3.amazonaws.com"
      s3_origin_config = {
        origin_access_identity = "s3_bucket_one"
      }
    }
  }

  default_cache_behavior = {
    target_origin_id       = "something"
    viewer_protocol_policy = "allow-all"
    allowed_methods        = ["GET", "HEAD", "OPTIONS"]
    cached_methods         = ["GET", "HEAD"]
    compress               = true
    query_string           = true
  }

  ordered_cache_behavior = [
    {
      path_pattern           = "/static/*"
      target_origin_id       = "s3_one"
      viewer_protocol_policy = "redirect-to-https"
      allowed_methods        = ["GET", "HEAD", "OPTIONS"]
      cached_methods         = ["GET", "HEAD"]
      compress               = true
      query_string           = true
    }
  ]
}

```
### Additional Notes
**Viewer SSL Certificate:** If you are using a custom domain, you can add the viewer_certificate configuration to link an AWS ACM certificate to your CloudFront distribution.
**Origins:** You can configure multiple origins in the origin block, including S3 buckets and custom HTTP origins.
**Cache Behavior:** The default cache behavior and ordered cache behaviors provide flexible caching options based on specific URL patterns.
This module simplifies managing CloudFront distributions with Terraform, allowing for scalable and customizable web delivery configurations.