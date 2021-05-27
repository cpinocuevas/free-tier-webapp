# AWS CloudFormation Template - Free Tier Web App

This CloudFormation template configures a two-tier VPC based on a set of input parameters, with a sample web app. The application will be based in an EC2 instance of type t2.micro with latest Amazon Linux 2 OS, bootstrap Apache/PHP, and install a simple address book web application, and an Amazon RDS MySQL database instance in free tier, i.e. of type db.t2.micro and with no Multi-AZ setup or read replicas. The WebTier Security Group will allow only SSH and HTTP connections to the web server (EC2 instance), and the DBTier Security Group will only allow the WebTier Security Group to initiate database connections to the RDS DB instance over the TCP port 3306.

## Default Topology

The template will launch a VPC with 2 public subnets and 2 private subnets. The EC2 instance will be launch in public subnet 1 and the RDS instance will be launched in private subnet 2. They other 2 subnets can be further used if high availity is required.

![VPC](images/vpc.png)

## Features

This template deploys:

* Two tiers. e.g. web tier (public), and database tier (private).
* Deploys subnets in 2 different Availability Zones (AZs). 
* Sample web app in an EC2 t2.micro
* MySQL database in a Single-AZ db2.t2.micro
* WebTier and DBTier Security Groups 

Disclaimer:

* If private subnets require outbound internet access, a NAT Gateway needs to be deployed. - It is not included in the Free tTier


## Usage

Create an AWS CloudFormation stack based on the parameters described in the next section and the template [`cf-template.yml`](cf-template.yml)

## Parameters

## Common Parameters

Use the following parameters to assign the overall CIDR block and name for the VPC.

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pVpcName`|Optional|Acts as the qualifier of the VPC name and is also used to qualify supporting resource names per AWS resource naming best practices.|`dev` is the default qualifier value and is prefixed by the value of `pSystem` to create the VPC name. For example, with default parameter values for `pSystem` and `pVpcName`, the VPC name will be `infra-dev`.|
|`pOrg`|Optional|Used to qualify IAM resource names per AWS resource naming best practices.|`example`|
|`pSystem`|Optional|Used to qualify IAM resource names per AWS naming best practices.|`infra`|
|`pCidr`|Required|CIDR block of the VPC|`10.1.0.0/20`|

### Tier Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pTier1Name`|Optional|Name of web tier. Used to qualify subnet names.|`public`|
|`pTier2Name`|Optional|Name of database tier. Used to qualify subnet names.|`private`|

### Subnet Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pTier1Subnet1Cidr`|Optional|CIDR block for Web Tier, Subnet 1. Required if creating Tier 1.|`10.1.0.0/24`|
|`pTier1Subnet2Cidr`|Optional|CIDR block for Web Tier, Subnet 2. Required if creating Tier 1.|`10.1.1.0/24`|
|`pTier2Subnet1Cidr`|Optional|CIDR block for DB Tier, Subnet 1. Required if creating Tier 2.|`10.1.3.0/24`|
|`pTier2Subnet2Cidr`|Optional|CIDR block for DB Tier, Subnet 2. Required if creating Tier 2.|`10.1.4.0/24`|

### Web App Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pKeyName`|Required|Name of an existing EC2 KeyPair to enable SSH access||
|`pLatestAmiId`|Required|Name of AMI to deploy the web server|`/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs`|

### Database Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pDatabaseName`|Required|Database name|`mydb`|
|`pMasterUsername`|Required|Username for MySQL database access||
|`pMasterPassword`|Required|Password for MySQL database access||

## Exported CloudFormation Stack Outputs

The following CloudFormation stack outputs are exported so that other stacks can import this data:

* `${AWS::StackName}::vpc-id`

* `${AWS::StackName}::${pTier1Name}-subnet-id-1`
* `${AWS::StackName}::${pTier1Name}-subnet-id-2`

* `${AWS::StackName}::${pTier2Name}-subnet-id-1`
* `${AWS::StackName}::${pTier2Name}-subnet-id-2`

* [http://${EC2Instance.PublicDnsName}/](http://${EC2Instance.PublicDnsName}/)
* `MyDB.Endpoint.Address`



