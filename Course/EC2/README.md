# CHAPTER 4 | EC2 The Backbone of AWS

Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers.

### [EC2 Options](https://aws.amazon.com/ec2/pricing/)

* [On demand](https://aws.amazon.com/ec2/pricing/on-demand/): You pay for computing capacity by per hour or per second depending on which instances you run.
* [Reserved Instance (RI)](https://aws.amazon.com/ec2/pricing/reserved-instances/): Provide a significant discount (up to 75%) compared to On-Demand pricing and provide a capacity reservation when used in a specific Availability Zone. You have to enter a contract.
* [Spot](https://aws.amazon.com/ec2/spot/): Amazon EC2 Spot instances allow you to request spare Amazon EC2 computing capacity for up to 90% off the On-Demand price.

  * If you terminate an instance, you will pay for the complete hour.
  * If Amazon terminates the instance, you won't pay for the complete hour.
  * The following are the possible reasons that Amazon EC2 might [interrupt your Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html):
    * Price – The Spot price is greater than your maximum price.
    * Capacity – If there are not enough unused EC2 instances to meet the demand for Spot Instances, Amazon EC2 interrupts Spot Instances. The order in which the instances are interrupted is determined by Amazon EC2.
    * Constraints – If your request includes a constraint such as a launch group or an Availability Zone group, these Spot Instances are terminated as a group when the constraint can no longer be met.
    Spot instances save up to 90% off on-demand instances
    Useful for computing that doesn't need persistent storage
    Can Use spot block to stop instances from terminating
    Spot Fleet is collection of spot instances and (optionally demand instances)



* [Dedicated Hosts](https://aws.amazon.com/ec2/dedicated-hosts/): Is a physical server with EC2 instance capacity fully dedicated to your use.

### [What's EBS](https://aws.amazon.com/ebs/)

Provides persistent block storage volumes for use with Amazon EC2 instances in the AWS Cloud. EBS is automatically replicated in a specific AZ

### [Amazon EBS Volume Types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)

#### SSD

SSD are good for random access.

* General Purpose SSD (GP2): General purpose SSD volume that balances price and performance. 3 [IOPS](https://en.wikipedia.org/wiki/IOPS) per GB, up to 10k IOPS
* Provisioned IOPS SSD (io1): Designed for IO intensive, use it if you need more than 10k IOPS

#### HDD (Magnetic)

Are great for sequential access (processing log files, bigdata work flows as an example).

* Throughput Optimized HDD (st1): Magnetic disk, this can't be a boot volume (root volume).
* Cold HDD (sc1): Lower cost storage, like file servers, can't be a boot volume.
* Magnetic (standard): Lowest cost per gigabyte of all EBS. It's bootable and it's from the previous storage generation.

### FSX

* FSX for Windows - centralized storage for windows based applications (sharepoint, SQL Server, Workspaces, IIS)
* FSX for Lustre - fast, high capactiy storage for ML/HPC (high performance computing), can store to s3
* EBS  - distributed, resilient storage for linux instances

### Launch an EC2 Instance - Lab

* Termination protection is turned off by default.
* The EBS root volume by default is deleted at termination.
* Default AMI's (provided by Amazon) can be encrypted.
* Additional volumes can be encrypted.

## Security groups - Lab

A security group acts as a virtual firewall for your instance to control inbound and outbound traffic.

* All Inbound traffic is Blocked by Default.
* All Outbound traffic is Allowed by Default.
* All security groups changes are applied immediately.
* Security groups are stateful. For example, if you allow the request to come in, automatically responses can go out even if you don't have anything on the outbound section of your security group.
* You can specify only allow rules, not deny rules.

## EBS Volumes & Encrypt Root Device Volume - Lab

* Instances and Volumes MUST be in the same AZ
* Snapshots exists in S3.
* Snapshots are a point in time copies of Volumes.
* Snapshots are incremental (only differences are saved)
* Snapshot of root volumes don't require the instance to be stopped (but less performance).
* _You can't delete a snapshot of an EBS volume that is used as the root device of a registered AMI._
* You can change EBS volumes size and type on the fly.
* To move an EC2 volume from one AZ/Region to another, take a snap or an image of it, then you can copy them to the new AZ/Region.
* Snapshots of encrypted volumes are encrypted automatically
* Volumes restored from an encrypted snapshot will be encrypted as well.
* You can share snapshots only if they are not encrypted, these snapshots can be made public.

### [AMI Types](https://aws.amazon.com/amazon-linux-ami/instance-type-matrix/)

* [EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html): Amazon EBS provides durable, block-level storage volumes that you can attach to a running instance
  * EBS takes less time to provision.
  * EBS volumes can be kept once the instance is terminated.
* [Instance Store / Ephemeral storage](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html): This storage is located on disks that are physically attached to the host computer

  * Instance Store Volumes can't be stopped, if the host fails, you lose data.
  * You can reboot the instance without losing data.
  * You can not detach Instance Store Volumes.
  * Instance store volumes cannot be kept once the instance is terminated.

### Hibernate

-Preserves in memory ram on EBS
Faster boot up, since OS doesn't reload
instance ram must be <150GB
Available for all OS
Can't hibernate longer than 60 days
Available for On-Demand and Reserved Instances

### Networking
* ENI - Basic Networking, can seperately manage prod and logging network
* EN Enhanced Network- For speeds b/t 10gbps - 100 gbps
* EFA Elastic Fiber Adapater - HPC, LM applications, OS bypass

### Components of HPC

Data Transfer
  Snowball, Snowmobile (terabytes/petabytes of data)
  AWS DataSync to store on S3, EFS, FSx for Windows, etc
  Direct Connect
  
Compute & Networking
  EC2 Instances
  EC2 Fleets (Spot Instances or Spot Fleets)
  Placement Groups
  Enhanced Networking SR-IOV (single root IO virtualization)
  Elastic Network Adapters or Intel VF interface
  Elastic Fabric Adapters (OS bypass)
  
 Storage
 Instance attached storage:
   EBS - Scale up to 64k IOPS with Provisioned IOPS
   Instance Store: Scale to millions of IOPS, low latency
 Network Storage
   S3: distributed object-based storage, not file system
   EFS: scale IOPS based on total size, has provisioned iops
   FSX for Lustre: HPC-optimzied distributed file system, millions of IOPS, backed by S3
   
   Orchestration & Automation
   AWS Batch
   AWS ParallelCluster
   
  
  
### CloudWatch - Lab

* Standard monitoring is 5 min and * Detailed monitoring is 1 min (you will be charged for it)
* Dashboard used to visualize what's happening with your AWS environment.
* Alarms can be set to notify when a specific threshold is hit
* Events can be used to perform actions when state changes happen in your AWS resources.
* Logs can be aggregated in a single place to better troubleshoot. Remember that you need to install an agent on the EC2 instance.
* By default, Matrics on EC2 instances are: CPU related, Disk related, Network related and Status check related.

Remember that:

* CloudWatch: is for monitoring resources
* CloudTrail: is for auditing

### IAM Roles with EC2 - Lab

* Roles avoid you to store credentials inside EC2 instances in order to communicate with other AWS services
* Roles can be assigned after an EC2 instance it has been provisioned
* Roles are universal, you can use them in any region.

### [EC2 Instance Metadata](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)

Within your local instance run:

 ```http://169.254.169.254/latest/meta-data```

 This will give you the top level metadata items, something like this.

 ```text
 [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
iam/
instance-action
instance-id
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/
```

If you want to access a specific item, simply add it at the end of your request:

```bash
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/ami-id
ami-12345678
```

There is also one URL for user data (gives bootstrap script you used)

```bash
[ec2-user ~]$ curl http://169.254.169.254/latest/user-data/
```

### [Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html)

You can launch or start instances in a placement group, which determines how instances are placed on the underlying hardware. When you create a placement group, you specify one of the following strategies for the group:

* Cluster – clusters instances into a low-latency group in a single Availability Zone.
* Spread – spreads instances across underlying hardware and can spread in multiple Availability Zones.
* Partition – spreads instances across logical partitions, ensuring that instances in one partition do not share underlying hardware with instances in other partitions.

Some notes about placement groups:
* Spread and Partitioned groups can be in multi-az, cluster can't
* The name you specify for a placement group must be unique within your AWS account.
* Only specific types of instances can be launched in a placement group. Homogenous instances recommended in clustered placement groups
* You can't merge placement groups.
* You can move an existing instance into a placement group. Instance must be stopped, then moved/removed from AWS SDK/CLI not Console


## [EFS](https://aws.amazon.com/efs/)

Amazon EFS provides scalable file storage for use with Amazon EC2. You can create an EFS file system and configure your instances to mount the file system

* Supports NFSv4.
* You only pay for the storage you use.
* Scale up to petabytes.
* Can support thousands of concurrent connections.
* Data is stored across multiple AZ.
* Read after write consistency.

## WAF 
Web application firewall that lets you monitor requests forwarded to cloudfront, alb, api gateway

abilities:

  * allow requests form all except blacklist
  * allow request from only whitelist
  * count requests that match properties specified

## Elastic Load Balancers

### [What's an Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)

Elastic Load Balancing automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, and others.

### [Types of loadbalancers](https://aws.amazon.com/elasticloadbalancing/features/#Details_for_Elastic_Load_Balancing_Products)

* Application Load balancers: Best for load balancing HTTP & HTTPS traffic. They operate at layer 7.
* Network Load Balancer: Loadbalancing TCP traffic where extreme performance is needed. They operate at layer 4.
* Classic Load Balancer: Legacy ELB, mostly operate at layer 4, but can go up to 7.

### X-Forwarded-For

(XFF) The HTTP header field is a common method for identifying the originating IP address of a client connecting to a web server through an HTTP proxy or load balancer.

### [Lanch configurations](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchConfiguration.html) and [Autoscaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)

* A launch configuration is an instance configuration template that an Auto Scaling group uses to launch EC2 instances
* An Auto Scaling group contains a collection of Amazon EC2 instances that are treated as a logical grouping for the purposes of automatic scaling and management.

Autoscaling group will automatically spread evenly on the number of instances across the AZ you selected once you configured it. So 3 AZ with 3 as group size, means 1 box in each AZ

### Load Balancers - Lab

* You must set up health checks in order to let the load balancer to understand if the instances are up and running
* Load balancers won't expose any IP, instead they expose DNS names.

```bash
dig +short MyClassicELB-57286656.eu-west-2.elb.amazonaws.com
35.176.153.196
35.176.224.44
```

_At the time of writing, the lab instructs you to create one EC2 instance and configure the load balancer to simply point against it.
My suggestion is to create 2 instances instead and change the `index.html` in something like `instance_1` and `instance_2` so you can see what box the load balancer decides to send your requests. Doing so, allows you to also confirm that if an instance is down, the load balancer automatically forwards traffic to the remaining one still online._
## [Lambda](https://aws.amazon.com/lambda/)

AWS Lambda lets you run code without provisioning or managing servers. You pay only for the compute time you consume - there is no charge when your code is not running.
Lambda basically is based on triggers.

You can use lambda in two ways:

* Event-driven based: Lambda runs your code based on events, such, new file on S3 or a new alarm on cloudwatch
* Compute-service based: Lambda runs your code based on HTTP requests using an API Gateway or API calls made using AWS SDKs.

AWS lambda, supports: C#, Node.js, Python, and Java

Lambda is charged as follow:

* First 1M requests per month are free. $0.20 PER 1M requests thereafter
* Duration: You are charged for the amount of memory you allocate on your functions. First 400,000 GB-seconds per month, up to 3.2M seconds of computing time, are free.
* Your functions can't go over 5 minutes in run-time.

_Make sure you have a look at what [CORS](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html) is._
