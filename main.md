# Identity Access Management (IAM)
IAM is a global service that allows us to manage users, groups, policies and roles. We can assign permissions to users or groups of users using Policies. Roles are policies that can be inherited by entities. They are necessary for services that run actions on our behalf (like EC2).

Within IAM, we can generate a Credential Report that lists the status of the credentials owned by the users in the account. We can also view the allowed services of any user with Access Advisor.

# Elastic Compute Cloud (EC2)
EC2 is a services that allows us to rent virtual machines. We can choose the operating system, compute power (CPU), RAM, storage and network card. We can configure an instance's firewall rules, its bootstrap script, etc.

## EC2 Purchasing Options
There are four options to rent EC2 instances.
1. On-Demand: Most expensive, billed by seconds used, minimum time billed is 60 seconds.
2. Reserved Instances: Commit to renting an instance for one or three years. Instance type, region, Availability Zone (AZ) and OS are fixed for this period. We get a discounted rate whenever we start an instance that matches the attributes of an active reserved instance.
3. Savings Plan: Commit to an hourly spend commitment between one or three years (i.e., $0.15/hour for one year). Instance family and region remain fixed.
4. Spot Instances: Define a maximum price you are willing to pay and compare that against the market's spot price. If the spot price is greater than our threshold, we lose the instance.
5. Dedicated Hosts: Reserve a physical server from AWS.

## EC2 Instance Storage

### Elastic Block Store (EBS)
We can attach EBS volumes to an EC2 instance for storage. EBS volumes are network drives (they are not physically attached to an instance) and are locked to an availability zone. To move a volume between AZs, we need to take a snapshot of the original volume and then create a new volume from the snapshot in the desired AZ. To move a volume between regions, we need to snapshot a volume, copy the snapshot to another region and then create a new volume from the snapshot. EBS volumes are useful because they allow us to persist our data. So if an instance fails, we can start where we left off by attaching the old instance's drive to the new one.

At the CCP level, EBS volumes can only be attached to a single EC2 instance.

### Instance Store
Since EBS volumes are not physically attached to EC2 instances, their performance is not as good as a physical drives. An Instance Store is a high-performance hardware disk that has better IOPS than an EBS drive. However, the data is lost if the EC2 is stopped or terminated.

### Elastic File System (EFS)
EFS is a network file system that can be mounted on multiple Linux EC2 instances at the same time. It is an improvement over EBS because it let's us share storage across multiple EC2 instances over multiple AZs (i.e., two EC2 instances can write on the same storage system and the data is synced).

### FSx
Used for launching third-party file systems on other operating systems (Windows and Lustre).

## Amazon Machine Image (AMI)
AMIs are customizations of EC2 isntances that specify the OS, configuration and software installed. AMIs are region-locked, so we must create a copy if we want to move an AMI across regions. AMIs are useful to automate the standardized creation of new instances. We can get faster boot times by starting instances with AMIs because all the software is pre-packaged.

To create an AMI, we can customize an EC2 instance, stop it and create an AMI from it. We can then lauch EC2 instances from this AMI.

## EC2 Image Builder
It is a service used automate the process of creating, maintaining and testing EC2 AMIs.

## Elastic File Service (EFS)


## Security Groups
We can define Security Groups to create a firewall around our EC2 instances. This way, we can control the traffic that comes in or out of our instances. The most common protocols to communicate with an EC2 instance are HTTP, HTTPS and SSH. We can use EC2 Instance Connect to connect to our instance through our browser without explicitly setting up SSH.

We can assign IAM Roles to our EC2 instances to be able to use AWS services from them.