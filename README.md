# cfn-matrix

This project has a CloudFormation template and scripts for installing Actian Matrix on Amazon EC2.  

## Prerequisites

1. A VPC with one public and one private subnet (based on routing table rules)
2. A NAT server in the public subnet that the private subnet is configured to pass outbound, external traffic to
3. A SSH Key-Pair for connecting with instances
4. An EC2 Placement Group to support high network throughput
5. A security group that allows all traffic between members of that security group, as well as inbound SSH traffic from your location
6. A security group for the NAT server that allows traffic from the previous security group so nodes can download/update packages from the internet.
7. An IAM Role with Read-Only access to S3 and EC2
8. An S3 bucket in your working region to copy the Matrix Installer and support files to

## Template Parameters

1. BucketName:  The default name is matrix53.   If the user creates a bucket with the same name they won't need to enter this.
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




