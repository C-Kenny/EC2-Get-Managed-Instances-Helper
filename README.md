## What is the EC2-Get-Managed-Instances-Helper?

When viewing managed instances via the EC2 Systems Manager (https://aws.amazon.com/ec2/systems-manager/), we have noticed that the managed instances count < total instances.

The EC2-Get-Managed-Instances-Helper tool will output a list of instances that are not "managed instances".

## What are managed instances?

Managed instances are configured by EC2Config / EC2Launch. Managed by the AWS EC2 Systems Manager


##Pre-requisites:

Systems manager has prerequisties for the EC2 Systems Manager to run.
See https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html#systems-manager-prereqs

Here's the snip for Linux:

```
Instances must run a supported version of Linux.

64-Bit and 32-Bit Systems
    Amazon Linux 2014.09, 2014.03 or later
    Ubuntu Server 16.04 LTS, 14.04 LTS, or 12.04 LTS
    Red Hat Enterprise Linux (RHEL) 6.5
    CentOS 6.3 or later

32-Bit Systems Only
    Raspbian Jessie
    Raspbian Stretch

64-Bit Systems Only
    Amazon Linux 2015.09, 2015.03 or later
    Red Hat Enterprise Linux (RHEL) 7.4
    CentOS 7.1 or later
    SUSE Linux Enterprise Server (SLES) 12 or higher
```


##How it is used:


##Glossary:
- EC2Config
    - Detailed view of config layout of AWS resources e.g. how resources are related to each other, includes relationships over time
    - EC2Config used for Windows Amazon Machine Images (AMIs)

- EC2Launch:
    - EC2Launch is a set of Windows PowerShell scripts that replaces the EC2Config service on Windows Server 2016 AMIs.
        - It performs steps like setting up computer name, wallpaper and instance info back to EC2 console

- Amazon EC2 Systems Manager
    - Helps with questions like: How many instances are running a particular version of software?
    - https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html?icmpid=docs_ec2_console

- Managed Instances
    - EC2 Systems that are currently in the EC2 systems manager

##Steps to resolve:
1. Create 2 EC2 instances on AWS
2. Setup EC2 systems manager
3. Add 1 EC2 instance to the systems manager
4. Find API call to get managed instances from EC2 Systems Manager
5. Find API call for all EC2 instances


##GOTCHAS:

- Instance profile role for Systems manager:
    - Role name: EC2_Systems_Manager_Role

- Given existing EC2 instances, follow http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role , to attach AWS Identity and Access Management (IAM) role to an instance.

- The current AWS tutorial excludes the final step of installing SSM agent
    - This is to happen post IAM role creation: https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html

- $ aws ec2 describe-instances
    - Returns all instances
- $ aws ssm describe-instance-information --instance-information-filter-list key=PingStatus,valueSet=Online
    - Returns instances where ssm is running on the Linux install

From here we focus on getting the returned values in JSON format, for ease of processing.

- Gets all running instances
    - `aws ec2 describe-instances -query 'Reservations[*].Instances[*].[Placement.AvailabilityZone, State.Name, InstanceId]' --output json 
    `
- Gets ssm managed instances
    - `aws ssm describe-instance-information --output json --query "InstanceInformationList[*]`

