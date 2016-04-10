**cfn-matrix -- AWS CloudFormation Template for Actian Matrix**
-----------------------------------

This repository contains an easy to use CloudFormation template for creating a multi-node 
[Actian Matrix](http://www.actian.com/products/big-data-analytics-platforms-hadoop/matrix-mpp-analytics-databases/) cluster on Amazon AWS.  

This template uses an CentOS 6.5 HVM AMI (ami-1b3a2e71) with support for enhanced networking.  At the moment this AMI is available only in the US-East-1 region.

The user can select the instance types for leader and compute nodes.
Instance storage (ephemeral) is used for database storage, so storage is limited by the instance type selection.  All storage costs are part of the instance cost.
The larger instance types will typically have more storage and support higher network speeds.

***Warning:***
The stacks created using this template use auto scaling groups and instance storage.   The EC2 instances that are created cannot be stopped using the EC2 console and are not suitable for always-on deployments of Matrix.
  
Actian Matrix is a high performance massively parallel (MPP), columnar, analytic database.

## Prerequisites

1. A VPC with one public and at least one private subnet (based on routing table rules).  The Matrix cluster will be provisioned and deployed in one of the private subnets.
2. A NAT gateway to allow internet access for the private subnet used for the Matrix cluster.
3. A SSH Key-Pair for connecting with instances.
4. An EC2 Placement Group to support high network throughput.
5. A security group that allows all traffic between members of that security group, as well as inbound SSH traffic from your location.
7. An IAM Role with Read-Only access to S3 and EC2.
8. An S3 bucket in your working region to store a copy of the Matrix Installer and script files.

## Stack Resources Used

CloudFormation will allocate a number of resources when creating a stack with this template.
The resource limits for the following resources on the AWS account will need to be high enough for the stack creation process to succeed. 

1. One VPC Elastic IP address
2. One Auto Scaling Group
3. One Launch Configuration
4. One instance for the Matrix leader node (type determined by LeaderInstanceType parameter)
5. Two or more instances for the Matrix compute nodes (type determined by ComputeInstanceType parameter)

These resources are released when the stack is deleted.

## Template Parameters

1. BucketName:  The name of the bucket containing the Matrix installer
2. ComputeInstanceType:   This now supports c3.xlarge, c3.2xlarge, c3.4xlarge, c3.8xlarge, d2.xlarge, d2.2xlarge, d2.4xlarge, d2.8xlarge, i2.xlarge, i2.2xlarge, i2.4xlarge, and i2.8xlarge instance types.
3. ComputeNodeNumber:  The number of Matrix compute nodes.   These nodes are separate from the leader node.
4. ComputeNodePctFS:   Percentage of available disk space on each disk used for a Linux file system.
5. IAMRole:   The default name is MatrixInstall.   If the user creates one with this name they won't need to enter this.
6. LeaderInstanceType:   Supports all of the same types as the ComputeInstanceType.
7. PlacementGroup:   The default name is MatrixPG.   If the user creates one with the same name they won't have to enter this.
8. PrivateSubnet:  The drop down gives a list of valid subnets.   The user should select a privite subnet that belongs to the same VPC and availability zone for the public subnet specified below.
9. PublicSubnet:   The drop down gives a list of valid subnets.   Needs to be in the same VPC and AZ as above.
10. SecurityGroup: Name of a security group with external SSH access and open access for all inbound traffic originating from both the specified public and private subnets.
11. SSHKeyName:  Name of an existing EC2 KeyPair to enable SSH access to the leader node instance.

## License ##

cfn-matrix is licensed under the Apache License Version 2.0. Details can be found in the file LICENSE.




