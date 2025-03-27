# Plantopedia AWS CloudFormation Template

## Overview

This AWS CloudFormation template deploys **Plantopedia**, a highly available web application designed to serve as a plant encyclopedia. The template provisions the following infrastructure components in an AWS account:

- A custom Virtual Private Cloud (VPC) spanning multiple Availability Zones (AZs) for high availability.
- Public and private subnets for network segmentation.
- An Amazon RDS PostgreSQL database instance with Multi-AZ failover for durability and reliability.
- AWS Secrets Manager to securely store and manage database credentials.
- Supporting networking resources such as an Internet Gateway, NAT Gateway, and route tables.

This setup ensures a secure, scalable, and resilient architecture suitable for production environments.

## Prerequisites

Before deploying this template, ensure you have the following:

1. An AWS account with sufficient permissions to create VPCs, subnets, RDS instances, and Secrets Manager resources.
2. The AWS CLI installed and configured (optional, for CLI-based deployment).
3. Basic familiarity with AWS CloudFormation and YAML syntax.

## Template Details

### Parameters

The template accepts the following customizable parameters (default values are provided):

- **VpcCidr**: CIDR block for the VPC (default: `10.0.0.0/16`).
- **PublicSubnet1Cidr**: CIDR block for the first public subnet (default: `10.0.1.0/24`).
- **PublicSubnet2Cidr**: CIDR block for the second public subnet (default: `10.0.2.0/24`).
- **PrivateSubnet1Cidr**: CIDR block for the first private subnet (default: `10.0.3.0/24`).
- **PrivateSubnet2Cidr**: CIDR block for the second private subnet (default: `10.0.4.0/24`).
- **PlantopediaDBUser**: Username for the RDS database (default: `plantuser`).
- **PlantopediaDBPassword**: Password for the RDS database (sensitive, no default; must be provided during deployment).
- **PlantopediaDBName**: Name of the database (default: `plantopedia`).
- **PlantopediaDBAllocatedStorage**: Storage size for the RDS instance in GB (default: `20`).
- **PlantopediaDBInstanceClass**: RDS instance class (default: `db.t3.medium`).

### Resources

The template provisions the following AWS resources:

1. **VPC and Networking**:
   - A VPC with DNS support and hostnames enabled.
   - An Internet Gateway attached to the VPC for public access.
   - Two public subnets and two private subnets across two Availability Zones.
   - A NAT Gateway in one public subnet for outbound traffic from private subnets.
   - Route tables and associations for public and private subnets.

2. **RDS PostgreSQL Instance**:
   - A Multi-AZ PostgreSQL database instance for high availability.
   - Deployed in private subnets with a dedicated subnet group.
   - Secured with a VPC security group allowing traffic on port 5432 (PostgreSQL) within the VPC.
   - Encrypted storage and a 7-day backup retention period.

3. **Secrets Manager**:
   - A secret storing the database username, password, and database name.
   - The RDS instance retrieves credentials dynamically from Secrets Manager.

### Outputs

After deployment, the stack provides the following outputs:
- **PlantopediaVPCId8**: The ID of the created VPC.
- **PlantopediaRDSInstanceEndpoint8**: The endpoint address of the RDS instance.

## Deployment Instructions

### Using the AWS Management Console

1. Save the template as `plantopedia-template.yaml`.
2. Log in to the AWS Management Console and navigate to the **CloudFormation** service.
3. Click **Create Stack** > **With new resources (standard)**.
4. Upload the `plantopedia-template.yaml` file.
5. Specify a stack name (e.g., `PlantopediaStack`).
6. Provide values for the parameters (e.g., `PlantopediaDBPassword`) or accept the defaults where applicable.
7. Review and create the stack. Deployment typically takes 10-15 minutes.
8. Once complete, check the **Outputs** tab for the VPC ID and RDS endpoint.

### Using the AWS CLI

1. Save the template as `plantopedia-template.yaml`.
2. Run the following command, replacing `<STACK-NAME>` and providing a password:

   ```bash
   aws cloudformation create-stack \
     --stack-name <STACK-NAME> \
     --template-body file://plantopedia-template.yaml \
     --parameters ParameterKey=PlantopediaDBPassword,ParameterValue=<YOUR-PASSWORD> \
     --capabilities CAPABILITY_NAMED_IAM
