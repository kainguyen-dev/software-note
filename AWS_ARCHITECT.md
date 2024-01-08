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
    - By default, all AWS accounts are limited to 5 Elastic IP addresses per Region

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

## 6. Load balancer:

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
        - route base on query string, header.
    - ALB can dynamic map port for soluiton like ECS
    - ALB can route to multiple target group
    - ALB even can route to on-premise and EC2-based target group
    - Good to know: 
        - True IP, port and proto is passed via: 
            - X-Forwarded-For
            - X-Forwarded-Port
            - X-Forwarded-Proto
    - You can't attach an Elastic IP address to Application Load Balancers.

- NLB: network load balancer layer 4 load balancer
    - Allow to:
        - Forward TCP and UDP 
        - Handle millions of request per seconds
        - Less lantency = 100 ms < (ALB is 400 ms)
        - NLB have one static IP per AZ (note per AZ, each AZ will have different IPs)
        - NLB is used for extreme performance
        - NLB is not free
    - NLB target groups:
        - EC2 instances 
        - IP addresses
        - Another ALB
    - NLB health check supports:
        - TCP
        - HTTP
        - HTTPs
    - Network Load Balancer has one static IP address per AZ and you can attach an Elastic IP address to it. Application Load Balancers and Classic Load Balancers have a static DNS name.

- GLB: gateway load balancer, use as a middleware for 3 party network system, for example: firewall, intrusion detection, deep packet inspection, ...
    - Operate at layer 3 (network layer)
    - Use GENEVE protocol on port 6081
    - Possible target:
        - EC2 instance
        - Private IPs

- Sessions stickiness:
    - All request will stick with the same client and target, this work for ALB, NLB and classic,
    - It use cookies for sessions stickiness
    - Cookie name:
        - Application base cookie:
            - Custom cookie:
                - Set by target
                - Must specify per each target group
                - Must not be AWSAPP, AWSALBAPP, AWSALBTG
            - Application cookie:
                - Generated by load balancer
                - Cookiename is AWSALBAPP
        - Duration base cookie:
            - Generated by the load balancer
            - Cookie name = AWSALB or AWSELB
        
- Cross-zone load balancing: load balancing across multiple AZ
    - ALB: enalbe by default (can disable at target group level), no charge across AZ
    - NLB: disable by default, cost money

- SSL/TLS: 
    - Server name indicator: multiple cert can be load to ALB, NLB it can detect which cert to get base on host name.

- Connection draining or deregistration delay: for ALB and NLB.
    - When instance is unhealthy or dead (draining state), it will not route traffic to that instance for a period of time (1 -> 3600 sec) default is 300 seconds.

- Auto scaling groups:
    - Used for scale load
    - ASG is auto register EC2 to load balancer
    - Set minimum/desired/max capacity
    - ELB can health check EC2
    - Attribute:
        - Launch template: contain AMI, instance type, EBS volumne, security groups, SSH key pair ,...
        - Have min/max capacity
        - Scale ASG base on CloudWatch arlam metric (CPU, custom metric)
    - Scaling policy:
        - Simple target policies: for example keep CPU usage around 40%
        - Step scaling policies: for example add 1 unit if CPU > 70%
        - Schedule action: for example at 1 AM add 1 unit for processing job
        - Preditive scaling: base in history usage
    - Metric for scaling:
        - CPU usage
        - Request per instance
        - Avg network 
    - Scaling cool down: after an increase or removal there will be a 300 cool down period.

## 7. RDS:

- RDS:
    - RDS advantage:
        - Automated provisioning, OS patching.
        - Continous patching, backup at timestamp.
        - Read replicas
        - Multi AZ back up
        - Scaling capability 
        - Storage back by EBS
    - RDS can't be SSH
    - Storage auto scaling:
        - Set by threshold
    - RDS read replicas for read scalability:
        - Up to 15 replicas
        - Three options to replicate:
            - Across AZ
            - Across Region
            - Same AZ
        - Replication is async
        - Replica can be promoted to their own DB
        - Application must update connection to leverage replicas.
    - RDS replication network cost:
        - Same region --> No fee
        - Cross region --> Have fee

    - RDS multi AZ: use for Discovery disaster recovery:
        - SYNC replication 
        - In another AZ, failover when AZ lost or lost connection
        - Master and DR replicas share the same DNS
        - Note: Read replicas can be set up as DR

    - RDS from single -> multi AZ: zero-down time operation.
        - Internally:
            - A snapshot is taken
            - A new DB is created from snapshot
            - New Sync connection create between master and DR 

    - Amazon Auroras:
        - "Cloud optmized" have better performance for PostGres and MySQL.
        - Storage grow increment from 10Gb -> 128 TB
        - Can have 15 replicas
        - Failover in Auroras is instaneous
        - Auroras cost more 20%
    
    - Auroras HA:
        - 6 Copies accross multiple AZ
        - Support cross region replication.
    - Auroras cluster: 
        - Have one write end point
        - Have read end point: load balancing between read-replicas.
    - Feature:
        - Auto failover
        - Auto scale
        - Back up
        - Back track: restore data in the past without backup.
    - Advanced concept:
        - Auto-scaling: read endpoint auto cover newly created replicas.
        - Custom endpoint: for analytical for example
        - Serverless: Good for infrequent unpreditable workload.
        - Global database:
            - 1 primary region (all read and write)
            - Up to 5 secondary regions, replication lag < 1 sec 
            - Promote 1 region take less than 1 min
        - ML:
            - SageMaker
            - Comprehend (sentiment analysis)
        - RDS backup:
            - Daily back up
            - 5 mins back up from transaction logs
            - Retention time = 1 - 35 days for automate back up
            - Retention time = infinite for manual back up
        - RDS clone: will be very fast, because they will share the same EBS volume, additional write/update will be kept seperate
        - RDS & Auoras security:
            - At-rest data encryption: database and replicas encryption using KMS - must be defined at launch time.
            - If data is unencrypted -> require take a snapshot and restore.
            - In-flight encryption: TLS-ready by default.
            - IAM Authentication: IAM roles to connect to your database.
            - Security group: control inbound-outbound.
            - No SHH access
            - Audit logs sent to cloud watch
    - RDS Proxy:
        - Create a proxy for RDS, manage connection pool, in order to reducing the stress on DB and minimize open connection.
        - RDS proxy must be access from a VPC.

    - ElastiCache:
        - Similar to RDS for manage relational database, ElastiCache is to get managed Redis or Memcached.
        - Using ElastiCache require heavy code change.
        - Security:
            - IAM 
            - Security group
            - User/password
            - SSL in-flight

## 8. DNS:

    - DNS Terminologies:
        - Domain Registar: route 53, go Daddy
        - DNS records 
        - Zone files: database contain DNS records
        - Name server: resolve DNS (Authorities and non-authorities)
        - Top level: .com, .us
        - Second level domain: .example
        - Sub domain: www
        - FQDN: www.google.com

    ![Alt text](image-14.png)

    - Route 53:
        - DNS of AWS.
        - Also a domain registar
        - Can check health of resource

    - Route 53 record:
        - Domain, sub-domain
        - Record type: A vs AAAA vs CNAME, NS:
            - A map to Ip4
            - AAAA map to Ip6
            - CNAME: map host name to another host name
        - Value
        - Routing policy
        - TTL

    - Host zones:
        - A container of record that define how to route traffic to domain and sub domain
        - There are two type of host zones:
            - Public host zones: allow all client from internet to query DNS
            - Private host zones (for routing within VPCs)
            - 0.5 per month per host zones

![Alt text](image-15.png)

    - Create record in route 53:
![Alt text](image-16.png)

    - CNAME vs alias:
        - CNAME: map an Root domain (fully test.mydomain.com) to a AWS resource.
        - Alias: map an Root domain or non root domain (like mydomain.com) to a AWS resource, also free of charge and health check, can't set TTL.
            - Target for Alias: ELB, Cloud front, API Gateway, Beanstalk, S3.
            - You can not set DNS alias for EC2 DNS
    
    - Routing policies:
        - Simple
            - Typically route traffic to a single resource, can specify multiple values in the same records --> random one will be chosen.
        - Weight
            - Control the % of request that go to each resources
        - Latency
            - Can be associate with health check to route to the nearest region.
            - Health check:
                - HTTP health check for PUBLIC records.
                - Type:
                    - Monitor endpoint:
                        - Interval 30s
                        - If 18 % report is healthy -> healthy
                        - We must allow request for health checker in firewall rule
                    - Calculate health check:
                        - Combine by AND, OR, NOT operator of multiple child health check
                        - For private resource, we must create cloud watch alarm assiciate with it.
        - Failover:
            - We have a active/passive (or disaster recovery instance), when main instance down, route 53 automatically change to secondary instance.
        - Geolocation
            - Route to when user actually located.
        - Geoproximity
            - Used to shift traffic from one AZ to another one
        - IP-based routing:
            - We provide a list of CIDRs (IP range) and their corresponding endpoint/location.
        - Multi-value routing: 
            - Use to route traffic to mutiple resources
            - Can associate with health check
        
    - Domain registar vs DNS service:
        - Domain registar is where you can buy a domain with fee.
        - DNS service is a database for you to manage your DNS record (domain -> IP).      
![Alt text](image-17.png)
        
        - How to use 3rd party (GoDaddy, ...) domain with route 53 ? 
            - Firstly, buy a domain
            - Secondly, create a host zone
            - Third, specify name server of created host zone in 3rd party domain registar
        
    - Route 53 can not replace Load balancer because of their incapacity of add/remove instance on the fly.

## 9. Solution architect dicussion:

- What time is it ?
![Alt text](image-18.png)

- Five pillar of a well architect application:
    - Cost
    - Performance
    - Reliability
    - Security
    - Operational excellence


- Elastic beanstalk:
    - Provide a developer centric-view of deploying application on AWS.
    - Environment:
        - Worker
        - Web server

## 10. S3

- S3 Introduction:
    - S3 is a backbone buidling block of many websites
    - Usage:
        - Backup and storage
        - Disaster recovery
        - Archive
        - Application hosting
        - Media hosting
        - Data lake and big data
        - Example: Nasdag store 7 years of data into S3
    - S3 allow file to store in bucket
    - Bucket name have to be unique (globally)
    - Bucket are regional level (can not transfer across region directly)
    - Key is the indentifier for object that stay in bucket:
        - Full path: s3://bucket_name/my_file.txt
    - Object:
        - Can be aquire using key
        - Max size = 5000 GB (5 TB)
        - If upload more than 5Gb, must use "multi part upload"
        - Object can be associate with:
            - Meta data: system data
            - Tag: user-defined data
            - Version ID
    - Bucket security:
        - User-based: access to bucket via IAM 
        - Resource-based: set up security for bucket cross account
        - ACL
        - Encryption: using encryption key
    - S3 can host static website and make them accessible on internet.
    - S3 Versioning:
        - You can version file in S3
        - It set up at bucket level
        - Same key will overwrite with the same version: 1,2,3 ... 
        - It is best practice to version bucket because of tracking change
            - Note: file that is not versioned before enable version will have version = null
    - S3 Replication:
        - Same region replication vs Cross region replication
        - Copying is async
        - After enable replication, only new item is replicated
        - If you want replicate exist object use S3 batch replication
        - Delete marker is disable by default
    - S3 Storage class:
        - General purpose:
            - 99.99% availability
            - Used for frequent access data
            - Low latency, high througput
            - Use in normal application
        - Standard infrequent access
            - Lower cost
            - Only 99.9% availability
            - Use case: DR, back up
        - One Zone-infrequent access
            - Only 99.5% availability
            - Datalost when AZ is destroy
            - Use case: back up for on-premise data
        - Glacier instant retrieval
            - Ms retrieval
            - Min storage duration is 90 days
        - Glacier flexible retrieval
            - Flexible retrieval time, 3-5 hours, 5-12 hours, ...
            - Min storage duration is 90 days
        - Glacier deep archive
            - Longer retrieval time 12 hours, 48 hours
        - Intelligent tiering
            - Move S3 class base on usage pattern
            - No charge for S3 intelligent tiering
            - Cycle:
                - Frequent access tier: 
                - Infrequent access tier: after 30 days no access
                - Archive instant access tier: after 90 days no access
                - Archive access tier
        - We can move S3 class manually or use S3 life cycle
    - S3 Durability and availability
        - High durability, 10,000,00 object have a chance loss in 10.000 years, durability is the same for all class
        - Availability is vary accross storage class
    - S3 Life cycle rule:
        - Moving between storage class can be automated using life cycle rules.
        - Transition actions: example: move to another storage class after 30 days
        - Expire actions: configure object to be remove after some time
    - S3 Analytics help to make storage analysis
    - Life cycle action include:
        - Move object between storage class
        - Expire/permanently delete non-current object
        - Delete object with delete marker
    - S3 requester pay:
        - Normally, bucket owner pay for storage and network cost, in requester pay, the requester pay for download network (must be authenticated)
    - S3 event notification:
        - ObjectCreated, ObjectRemoved, ObjectRestore, Replication, ... to send to queue notification.
        - In order to SNS, SQS receive notification from S3, it needs resources policy attach that allow to consume S3 event.
![Alt text](image-19.png)
    - S3 baseline performance:
        - Low latency 100-200 ms
        - Performance can achieve at least 3500 req/sec for PUT/POST/DEL operation, and 5000 req/sec for GET/HEAD
        - How to optimize s3 performance:
            - Multi-part upload: recommand for file 100 mb, must use for file > 5Gbs 
            - S3 transfer acceleration: transfer s3 data to an edge location than forward to target location.
            - Request byte-range, request from start -> end number of byte, parallelize request by specific byte range.
            - S3 select and glacier select: utilize server-filtering
            - S3 batch operation
![Alt text](image-20.png)
    - S3 Security: 
        - Object encryption: There are 4 method to encryption of S3:
            - Server-side encryption:
                - Encryption using S3 AWS managed key: 
                    - Key is owned and managed by AWS, encryption type = AES-256
                - Encryption using KMS:
                    - Encryption is managed by KMS, user control the key, to read the object it is needed to have access to both the object and the key
                    - Limitation:
                        - KMS key have limit quota of 5500/10000/30000 req per seconds
                - Encryption using customer key
                    - Upload and retrieval require both encryption and decryption key.
            - Client-side encryption
        - S3 CORS: cors = scheme + domain + port
            - We can allow all origin with "*"
            - Before any "read" request, server is make a small OPTION request with "Host" and "Origin" to see whether web server with origin is allow retrive such request.
            - CORS prevent:
                - Cross-Site Request Forgery
                - Scripting Attacks
![Alt text](image-21.png) 
        
        - S3 access log:
            - DO NOT set the place to store access log is its own bucket, it will create an infinite looping
        - S3 pre-singed log:
            - Given a particular file a URL for external to access for a period of time.
        - S3 glacier vault lock and object lock:
            - Adopt a WORM
            - Create a vault lock policy
            - Lock for future edits
            - Helpful for compilance data
            - Impossible to delete before expire date.
        - S3 Access point:
![Alt text](image-22.png)
        - S3 Object lambda:
            - Use lambda to change the object before it retrieved by called application
![Alt text](image-23.png)

## 11. Cloud Front
    - Cloud front = content delivery network. Improve read performance, content is cached at the edge.
    - Cloud front have about 216 point of presence globally.
    - DDos Protection, integration with Shield.
    - S3 bucket in cloud front is used for distributing file and caching them at the edge.
    - Cloud front origin can be:
        - ALB
        - EC2 instance response
        - S3 website
![Alt text](image-24.png)
    - Cloud front origin with s3 as origin
![Alt text](image-25.png)
    - Cloud front and S3 cross region replication difference:
        - Cloud front: 
            - leverage existing edge location
            - file are cached with TTL
            - best use for static content
        - S3 replication:
            - Must set up manually for each region
            - File update are near real time
            - Read only
            - Best use for dynamic content
![Alt text](image-26.png)

    - Cloud front Geo restriction:
        - Restrict who can access distribution
            - Allowlist: allow user to access content if come from specific country.
            - Blocklist: prevent user from accessing content if come from specific country.
            - "Country" is determine using 3rd party database.

    - Cloud front price class:
        - All around the world
        - Use North America, Europe, Asia, Middle East, and Africa
        - Use only North America and Europe
    
    - Cloud front invalidation:
        - We can specify a "/path/*" to invalidate all file in that path.
    
    - AWS global accelerator: 
        - Unicast vs Anycast:
            - Unicast: server share the same ip
            - Anycast: server share different ip
        - Multiple Edge location with the same IP will be spread around the world, request travel to the nearest one -> to ALB
![Alt text](image-27.png)
        - AWS global accelerator vs Cloud front:
            - both are leverage the AWS network and can be used to prevent DDos
            - Difference:
                - Cloud front: use for boost performance and response by cache at edge location
                - Global accelerator: use as a proxy network from edge location to origin

## 12. AWS Snow
- AWS Snow is a portable device to collect and process data at edge and migrate data to AWS
    - Data migration: Snowcone, Snowball edge, Snow mobile
    - Edge computing: Snowcone, Snowball edge
- Challenges with normal migration:
    - Limit connect, bandwidth, network cost
    - If it take more than a week to transfer data --> use aws snow
- Use case: AWS snow: large data migration, DC decomission.

- AWS FSx:
    - Use for launching high-performance file system in FSx

- AWS FS for window:
    - Support SMB protocol, Window NTFS
    - Microsoft active directory, ACLs, quotas.
    - Can be mounted on EC2 instance.
    - Scale up to 10s of GB, million of IOPs,

- AWS FS for Lustre
    - Parallel file distributed system
    - use for parallel computing, machine learning computing, video processing
    - Storage option:
        - HDD
        - SSD
    - S3 integration:
        - Read and write back

- Hybrid cloud for storage:
    - Part is on cloud
    - Part is on on-premises

- Reasons:
    - Long cloud migration
    - Law compliance
    - IT strategy

- AWS storage gateway is a solution for this:
    - Use to bridge data between on-premise and cloud data
    - Type of gateway:
        - S3 file GW 
        - FSx GW
        - Voluume GW 
        - Tape GW

- AWS S3 File gateway:
    - Configured bucket are accessible using NFS and SMB
    - Most recently used data is cached 
    - Support for S3 standard, One zone A, Standard IA, intelligent tiering
    - Bucket using IAM role for File GW

![Alt text](image-28.png)

- AWS FSx File gateway:
    - Native access to Amazon FSx
    - Local cache for Infrequent access data

![Alt text](image-29.png)

- Volumne gateway:
    - S3 storage back by EBS
    - Cached volume: low latency access to most recent data
    - Stored volume: entire dataset is onpremise, scheduled backup to S3

![Alt text](image-30.png)

- AWS transfer:
    - We want to use FTP to transfer data to S3 and EFS
    - Support protocols:
        - FTP
        - FTPs
        - SFTP
    - Pay per hour and network transfer
    - Integrate with authentication system (Okta, Microsoft active directory, LDAP, ...)

- DataSync: 
    - Use to move large amount of data from one location to another one.
        - On premises to cloud of AWS - need agents
        - AWS service to another AWS service - no agent is need
    - Can synchronize between
        - S3
        - EFS
        - FSx
    - Replication task can be scheduled hourly, daily or weekly
    - File permission and meta data is preserved.
    - One agent task can be use 10 Gbps

![Alt text](image-31.png)

![Alt text](image-32.png)

- Storage comparision:
    - S3: object storage
    - EBS: network storage attach to EC2, similar to a USB stick 
    - Instance storage: have the highest Iops, physical storage of EC2
    - EFS: network transfer file system can attach to multiple EC2
    - FSx for window: file system for window
    - FSx for Lustre: high computing file system for linux
    - FSx for NetApp: high OS compability
    - FSx for OpenZFS: Manage ZFS file system
    - Storage GW: use to bridge data between on-premise and cloud
    - Transfer family: allow upload to S3, EFS via FTP, SFTP, FTPs
    - Snow family: transfer large data that network is not possible
    - DataSync: scheduled job to transfer data between on-premise to AWS or AWS to AWS

## 13. SNS, SQS, Kinesis, Active MQ

- SQS:
    - Simple queueing service
![Alt text](image-33.png)
    - Fully managed service, used to decouple service.
    - Attribute:
        - Unlimited throught put, unlimited number of message in queue.
        - Default retention of message: 4 days, maximum of 14 days
        - Low latency: < 10 ms
        - Message size = 256 Kb
        - Can have duplicate message (at least one delivery, occasionally)
        - Can have out of order message
    - SQS producer:
        - Produced using SDK
    - SQS consumer:
        - Polling message (up to 10 messages at once)
        - Processing message then delete using DeleteMessage API
![Alt text](image-34.png)
    - SQS with auto scaling group:
        - Group of consumer can watch a specific arlam Like `NumberOfMessages` and size up or down number of Ec2 instance.
![Alt text](image-35.png)
    - SQS to decouple between application tiers
    - SQS Security:
        - In-flight encryption
        - At rest encryption using KMS key
        - Client-side encrytion if client want to perform client side encryption/decryption
    - Access control: IAM policies to regulate access to SQS API.
    - SQS Message visibility timeout:
        - When consumer by a consumer, it become invisible for a few seconds to other consumer, by default time out = 30s
![Alt text](image-36.png)
        - If consumer is not processed within the visibility timeout, there is a chance, it will be processed twice
        - Consumer need to use ChangeMessage visibility API to get more time.
        - If visibility is high, consumer crashes, re-processing will take time.
        - If visibility is low, we can get duplicates
    - SQS long polling:
        - When consumer request a message from the queue, it can optionally 'wait' for message to arrive if there are none in the queue
        - Long polling can decrease number of API call while increasing the efficiency and latency of application.
        - Wait time can be 1 -> 20 seconds
        - Long polling can be enable at queue level or at API level using WaitTimeSeconds
    - SQS FIFO:
        - Gurantee order but have a limit throughtput of 300mg/seconds, exact-one capability (remove duplicates)
        - Message are consume in order
    - SQS with ASG:
![Alt text](image-37.png)
    
    - If the load too big, some transaction may be lost, to handle this, we use SQS as a data buffer layer:
![Alt text](image-38.png) 


    - SNS: Simple notification service:
        - One message to multimple recceiver
        - Event producer send one message to SNS topic
        - Event receiver listen SNS topic notificatio
        - Limtiation is 12.5 million subcriber per topic. Max 100.000 topic max
        - Subcriber can be:
            - SNS
            - HTTPs end point
            - SMS and phone notification
            - Lambda
            - Emails 
        - Topic publish:
            - Create topic, create subcription -> Publish to topic
        - Direct publish:
            - Create topic
            - Create platform endpoint
            - Publish to specific endpoint
        - Security:
            - In-flight encryption
            - At-rest encryption
    
    - SNS and SQS fanout pattern