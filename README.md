# Polyspace Access on Amazon Web Services (Linux VM)

# Requirements

Before you begin, check that you have the following:

* A valid Polyspace Access™ license. See the MathWorks™ [license center](https://www.mathworks.com/licensecenter/).

* An Amazon Web Services™ (AWS) account configured with the required permissions. To view which permissions are required, see this [example policy](prerequisites/polyspace-access-on-aws-iam-policy.json).  
To learn more about the services used, see [Learn About Cloud Architecture](#learn-about-cloud-architecture).

* An SSH Key Pair to establish a secure SSH connection with your AWS instance in our chosen region. To create an SSH key pair, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html).

# Costs
You are responsible for the cost of the AWS services used when you create cloud resources using this guide. Resource settings, such as instance type, will affect the cost of deployment. For cost estimates, see the [pricing pages](https://aws.amazon.com/pricing/) for each AWS service you will be using. Prices are subject to change.

# Introduction
This guide will help you automate the process of deploying Polyspace Access on a Linux virtual machine, using Amazon EC2 resources with your AWS account.  
For information about the architecture of this solution, see [Learn About Cloud Architecture](#learn-about-cloud-architecture).

Use this reference architecture to control every aspect of your cloud resources.

# Deployment Steps

To view instructions for deploying the Polyspace Access reference architecture, select a Polyspace release:

| Release |
| ------- |
| [R2022b](releases/R2022b/README.md) |
| [R2023a](releases/R2023a/README.md) |
| [R2023b](releases/R2023b/README.md) |
| [R2024a](releases/R2024a/README.md) |

# Update Notes
The template does not currently support automatic upgrades. It is possible to manually upgrade to a newer release and retain all user data, settings, and SSL certificates.

# Learn About Cloud Architecture
[Polyspace Access](https://www.mathworks.com/products/polyspace/static-analysis-notes/polyspace-access-team-collaboration.html) includes database and web server services that allow you to store, view and manage analysis results generated with [Polyspace Static Code Analysis tools](https://www.mathworks.com/products/polyspace.html).

AWS is a set of cloud services which allow you to build, deploy, and manage applications hosted on Amazon’s global network of data centers. This guide will help you launch a Polyspace Access instance using compute, storage, and network services hosted by AWS. To find out more about the range of AWS cloud-based offerings, see [cloud-based products offered by AWS](https://aws.amazon.com/products/).

You can use the AWS Management console to create, manage, and delete the services that you launch on AWS. For more information, see [AWS Management Console](https://aws.amazon.com/documentation/awsconsolehelpdocs/).

You use AWS CloudFormation templates to create Polyspace Access and the resources it requires. See also [AWS CloudFormation templates](https://aws.amazon.com/cloudformation/). This diagram illustrates the cloud architecture created by the template. For more information about each resource see the [AWS CloudFormation template reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html).

![Cluster Architecture](img/Polyspace_Access_in_AWS_architecture.png?raw=true)

### Networking Resources
|Resource                                      | Description|
|----------------------------------------------|------------|
|Security groups<br/> **`AWS::EC2::SecurityGroup`**| Defines the ports that are opened for ingress to the service.|  
|Internal security group traffic rule<br/>**`AWS::EC2::SecurityGroupIngress`**| Opens access to network traffic between service and license manager.|
|Elastic IP address <br/>**`AWS::EC2::EIP`**| Specifies an elastic and permanent public IP address to connect to the service.|
**`AWS::EC2::EIPAssociation`** | Links the elastic IP address to the instance running Polyspace Access.|



### Instances
|Instance             | Description|
|---------------------|------------|
| Polyspace Access instance<br/>**`AWS::EC2::Instance`**| By default, the user data is stored on a separate EBS volume mounted at `/mnt/database`. Communication between users and the node is secured using SSL.|
| Database volume<br/>**`AWS::EC2::Volume`**| A separate EBS volume to store the database. Choose an existing database volume or leave empty to create a new one. |
| Database mount point <br/>**`AWS::EC2::VolumeAttachment`**| The mount point for the database volume, specified as `/dev/sdh`. Depending on the OS, the mount point might be converted to `/dev/xvdh` on the instance.|
|License Manager instance (Optional) <br/>**`AWS::EC2::Instance`**| If you already have a license manager server, deselect this instance while you configure the template. <br/>The license manager instance is created by a nested CloudFormation stack. See [Nested Stacks](#nested-stacks).|
|IAM role for EC2 instances<br/>**`AWS::IAM::Role`**| This role allows you to access Amazon S3 from services running in EC2. |
|Instance profile for EC2 instances<br/>**`AWS::IAM::InstanceProfile`**| Profile that associates EC2 instance with IAM role profile for the EC2 instances that associates them with the IAM role above.|


### S3 bucket 
|| |
|-------------------------------------|-------------------| 
|**`AWS::S3::Bucket`**    |Facilitates the sharing of the auto-generated user accounts and the certificate with the admin and users. This data is encrypted in the bucket using server-side encryption.
### Lambda
Deletes all objects from the S3 bucket when the stack is deleted.

### Nested Stacks
If you select the option "**create license manager**", this template creates a child stack based on `license-manager-for-matlab-on-aws`. Additional resources might be created during this process. See [license-manager-for-matlab-on-aws](https://github.com/mathworks-ref-arch/license-manager-for-matlab-on-aws).

# Technical Support
If you require assistance or have a request for additional features or capabilities, please contact [MathWorks Technical Support](https://www.mathworks.com/support/contact_us.html).
