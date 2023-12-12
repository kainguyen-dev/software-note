# Cloud Architect:

## 1. AWS Fundemental
- AWS Region: 
    - Example us-east-1 ...
    - Region is a cluster of data center.
    - How to choose region:
        - Compliance
        - Latency
        - Pricing

- AWS AZ: 
    - Each region have different AZ (us-east-1a)
    - Use to prodvide redundancy.

- AWS Edge location

## 2. IAM and groups:
- IAM service is global
- One IAM can belong multiple groups
- Group can not contain sub-group

- Policy: file JSON describe permission to:
    - USER
    - GROUP
- Policy example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```
- IAM inheritance: 
    - User inside a group have all permission of that group.

- Permission Structure:
    - Version
    - ID
    - Array of statements:
        - Effect: allow/not allow
        - Principal: which account affect
        - Actions: example PUT object/ GET object
        - Resource: s3 bucket

- Roles:
    - Roles is simply a group permission for AWS service on our behalf.
    - For example: lambda-service-role have permission to access VPC, EC2FullAccess ...

- IAM Best practice:
    - Don't use root account
    - User belong to group have all inheritance permission of that group


## 3. EC2: 
- EC2 Instance type:
    - General purpose
    - RAM optimise: node in Spark
    - Storage optimise: OTLP system
    - Compute optimise: use for batch process, video encoding, decoding ... 

- Security Group (fire wall):
    - Configuration of inbound and outbound networking, simply a firewall

- EC2 Purchasing model:
    - Pay as you go
    - Reserving: pay an upfront amount
    - Saving plan: similar to reserving, but need commit at least X$/hour (lock per region)
    - Spot instance: huge discount but can be terminate at any point, not suitable for critical task
    - Dedicated host: offer a physical host for ec2

- Spot instance:
    - Can have 90% discount
    - Define max spot price and get the instance working while spot price < MAX spot price
    - If current price > MAX, we need terminate or stop spot instance

## 4. EC2 Associate:

- Elastic IP:
    - If we need a fix IP for our instance, we need elastic IP

- EC2 Placement group:
    - Placement strategy:
        - Cluster: multiple instance on the same AZ (same rack, same AZ, have low latency 10Gps network)
        - Spread: spread instance across multiple AZ (minimize failure, limit 7 instance per AZ)
        - Partition: divide ec2 into multiple partions base on racks (multiple partition on multiple AZ), each partition is a rack

- Elastic Network interface (ENI):
    - Logical component in a VPC that represent a virtual network card.
    - 1 ENI can comprise of:
        - Multiple private IPs
        - 1 Public IP
        - 1 MAC address
        - 1 or more security groups
    - Elastic Network Interfaces (ENIs) are bounded to a specific AZ. You can not attach an ENI to an EC2 instance in a different AZ.

- EC2 Hibernate:
    - Stop: instance disk is kept intact until next start
    - Terminate: instance disk will be removed
    - Hibernate: RAM is preserved on EBS volumne when stop, instance can not hibernate more than 60 days

## 5. EC2 Instance storage:

- EBS: is a volumne network drive you can attach to your instance while they run.
- It allow instance persist data, even after termination, only one EBS can attach to one EC2 at the time.
- Think of them as network USB sticks
- Belong to AZ, to move we can take snap shot.
- Delete on Termination: remove EBS along side with EC2

- EBS snap shot feature:
    - EBS snap short archive: snapshot can be move to cheaper tier
    - Recycle bin for snap shot
    - Fast snapshot restore

- AMI: Amazon machine image, customization of EC2
- AMIs are built for a specific AWS Region, they're unique for each AWS Region. You can't launch an EC2 instance using an AMI in another AWS Region, but you can copy the AMI to the target AWS Region and then use it to create your EC2 instances.

- EC2 Instance Store: ephemeral storage, refers to the temporary block-level storage that comes with certain types of Amazon Elastic Compute Cloud (EC2) instances. This store is not persisted. 
- EC2 Instance store != EBS 
- EC2 Instance Store provides the best disk I/O performance.

- EBS volumne type:
    - General purpose (gp2/gp3):
        - Cost effective, low latency
        - Range from 1 GB to 16 TB
        - gp2 size of volumne and iops are linked, in gp3 iops, throught put can be scaled independently.
        - Provision IOPS: when we need iops > 16k -> we need to switch to io1/io2
    - High performance (io1/io2)
        - Max PIOPS: 64k
        - Support multi attach
    - Low cost (st 1):
        - Can not be boot volumne
        - Throughput optimize
    - Lowest cost (sc 1):
        - For data that infrequent access

- EBS can be choosen by:
    - Size
    - Throughput
    - IOPs

- With IOPS of 310,000 only solution is to used EC2 Instance store.

- EBS multi-attach:
    - attach the same ebs to multiple ec2 in the same AZ
    - only allow for io1/io2

- To create new EBS encrypt from EBS unencrypt, create a encrypt snap shot and re-attach to ec2

- EFS: a network file system:
    - EFS work with ec2 in multi-AZ
    - Use case: content management, web servining ...
    - Compatible with Linux based AMI
    - Performance mode:
        - General: 
        - Max IO
    - Throughtput mode:
        - Bursting: 50Mb/s -> 100Mb/s
        - Provisoned: set a fix throughput
        - Elastic: automatically scale base on workload
    - Storage tiers:
        - Standard for frequently access
        - Low cost option
    - Availability:
        - Standard: for multi-AZ (regional)
        - One AZ: for development, low cost option access.
    - When create EC2: we can attach it to file system
    - We can use EFS as a way to share file data between different AZ

- EFS vs EBS:
    - EBS: 
        - attach to one instance
        - lock at AZ level
        - IO increase when size increase
        - az migration step:
            - take a snapshot
            - copy to another AZ
        - get terminated by default along with EC2
    - EFS:
        - share across multiple AZ
        - have a higher price 
    
- ELB: 
    - Classical load balancer
    - Gateway load balancer 2020
    - Network load balancer 2017
    - Application load balancer 2016

- ALB: layer 7 load balancer:
    - Route traffic to multiple instance ec2
    - Support HTTP2/web socket
    - Support redirect
    - Route table to different groups:
        - base on path
        - route base on host name
        - route base on query string, 