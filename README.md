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
7. An IAM Role with AmazonEC2ReadOnlyAccess and AmazonS3ReadOnlyAccess policies attached is needed for the installation of Matrix.   The same role is used for the s3fs-fuse filesystem, 
so if you need to create files using s3fs the role should include the AmazonS3FullAccess policy.
8. An S3 bucket in your working region to store a copy of the Matrix Installer and script files.
9. An optional S3 bucket to mount as a s3fs filesystem

### Creating the Installer S3 Bucket

The Matrix installer and the helper scripts are copied from a S3 bucket visible to the account used to run the template.  The 
installer file must be in the root of the bucket and the scripts must be copied to a folder called *scripts*.

![Installer S3 Bucket](https://raw.githubusercontent.com/ActianCorp/cfn-matrix/master/S3_Management_Console.png)

The installer comes from the [Actian Electronic Software Distribution (ESD)](http://esd.actian.com/) site.  You should download the *Self extracting Installer for RHEL 6 Matrix only installation*
version of the installer.  The current helper scripts work with Matrix versions 5.3 and later.

Before the installer is uploaded to the S3 bucket you will need to extract the *.run* file from the compressed tar file.   The *.run* file is then 
uploaded to the S3 bucket.

The steps for creating the installer S3 bucket:

1. Download a copy of the installer from ESD
2. Uncompress and untar the installer
3. Create the S3 bucket
4. Create the *scripts* folder
5. Upload the installer *.run* file to the bucket
6. Upload stage-one-install.exp, stage-two-install.exp and stage-two-install-wrapper to the *scripts* folder

## Stack Resources Used

CloudFormation will allocate a number of resources when creating a stack with this template.
The resource limits for the following resources on the AWS account will need to be high enough for the stack creation process to succeed. 

1. One VPC Elastic IP address
2. One Auto Scaling Group
3. One Launch Configuration
4. One instance for the Matrix leader node (type determined by LeaderInstanceType parameter)
5. Two or more instances for the Matrix compute nodes (type determined by ComputeInstanceType parameter)

These resources are released when the stack is deleted.

## Creating a Matrix Stack

The easiest way to create a new stack is to use the [CloudFormation Console](https://console.aws.amazon.com/cloudformation/home).
From the console use the *Create New Stack* button.  This will take you to the *Create stack* dialog.  From this dialog select *Upload a template to Amazon S3* option and use the
*Browse...* button to locate and upload a copy of *cfn-matrix.template*.  After you upload the template and hit the *Next* button you should see the following dialog.

![Create A New Stack Dialog](https://raw.githubusercontent.com/ActianCorp/cfn-matrix/master/Create_A_New_Stack.png)

All of the input fields with the exception of the *s3fs Bucket Name* field are required.  Enter all of the values and use the *Next* button to get to the *Review* dialog.
From the *Review* dialog push the *Create* button to create the stack.

## Template Parameters

The template defines the following parameters.   These names can be used when the template is used with the AWS command line interface.

1. InstallerBucketName:  The name of the bucket containing the Matrix installer and helper scripts
2. ComputeInstanceType:   This now supports c3.xlarge, c3.2xlarge, c3.4xlarge, c3.8xlarge, d2.xlarge, d2.2xlarge, d2.4xlarge, d2.8xlarge, i2.xlarge, i2.2xlarge, i2.4xlarge, and i2.8xlarge instance types.
3. ComputeNodeNumber:  The number of Matrix compute nodes.   These nodes are separate from the leader node.
4. ComputeNodePctFS:   Percentage of available disk space on each disk used for a Linux filesystem.
5. IAMRole:   The default name is MatrixInstall.   If the user creates one with this name they won't need to enter this.
6. LeaderInstanceType:   Supports all of the same types as the ComputeInstanceType.
7. MatrixInstallFile: The name of *.run* file to use from the installer bucket.
8. PlacementGroup:   The default name is MatrixPG.   If the user creates one with the same name they won't have to enter this.
9. PrivateSubnet:  The drop down gives a list of valid subnets.   The user should select a privite subnet that belongs to the same VPC and availability zone for the public subnet specified below.
10. PublicSubnet:   The drop down gives a list of valid subnets.   Needs to be in the same VPC and AZ as above.
11. SecurityGroup: Name of a security group with external SSH access and open access for all inbound traffic originating from both the specified public and private subnets.
12. SSHKeyName:  Name of an existing EC2 KeyPair to enable SSH access to the leader node instance.
13. s3fsBucketName (optional): The name of a S3 bucket to mount as a filesystem.   The filesystem mount is done for each of the nodes at */mnt/s3/s3fsBucketName*.

## License ##

cfn-matrix is licensed under the Apache License Version 2.0. Details can be found in the file LICENSE.




