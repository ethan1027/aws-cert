## EBS
- Network drive you attach to One instance only
- Linked to a specific availability zone
- volumes can be resized

## EBS Volume Types
- gp2: General Purpose Volumes (cheap)
  - 3 IOPS/GB minimum 100 IOPS, burst to 3000 IOPS, max 16k IOPS
  - 1 GB - 16 TB, +1 TB = +3000 IOPS
- io1: Provisioned IOPS (expensive)
  - Min 100 IOPs, max 64k IOPS or 32k
  - 4 GB - 16 TB. Size of volume and IOPS are independent
- st1: Throughput Optimized HDD
  - 500 GB - 16TB, 500 MB/s throughput
- sc1: Cold HDD, Infrequently accessed data
  - 250 GB - 16 TB, 250 MB/s throughput

## EBS - RAID configurations
- RAID 0 (add)
- RAID 1 (mirror)

## EBS Snapshots
- Incremental - only backup changed blocks
- EBS backups use IO and you shouldn't run them while your application is handling a lot of traffic
- Snapshots will be stored in S3 (but you won't see them)
- Not necessary to detach volume to do snapshot but recommended
- Can copy snapshots across region
- Can make Image (AMI) from snapshot
- EBS volumes restored by snapshots need to be pre-wamred (using fio or dd command to read the entire volume)

## EBS vs Instance Store
- Some instance do not come with Root EBS volumes. Instead, they come with Instance Store
- Instance Store is physically attached to the machine (EBs is a network drive)
- Pros of instance stores
  - Better IO performance
  - Good for buffer/cache/scratch data temp content
  - Data survives reboots
- Cons of instance stores:
  - On stop or termination, the instance store is lost
  - Can't be resized
  - backups must be operated by the user

## EFS - Elastic File System
- Use cases: content management, web serving, data sharing, Wordpress
- Compatible with Linux based AMI (not Windows), POSIX-compliant
- Uses nFSv4.1 protocol
- Uses security group to control access to EFS
- Encryption at rest using KMS
- Can only attach to one VPC, create one ENI per AZ

## EFS - Performance and Storage Classes
- EFS Scale
  - 1000s concurrent NFS client, 10 GB/s througput
  - Grow to Petabyte scale network file system
- Performance mode (set at EFS creation time)
  - General purpose: latency-sensitive use cases (web server, CMS)
  - Max I/O - higher latency, higher throughput, highly parallel (big data, media processing)
- Throughput Mode
  - Bursting Mode: common for FS (intensive work, then almost nothing), linked to FS size
  - Provisioned IO mode: high throughput to storage ratio - expensive
- Storage Tiers: Standard, Infrequent access

## EFS VPC Peering
- Work within VPC
- EC2 in another VPC can access through VPC peering
- On-prem server can access through Direct Connect or Site-to-Site VPN using IPv4 (not DNS)

## S3
- Object storage, serverless, unlimited storage, pay-as-you-go
- Good to store static content (image, video files)
- Acess objects by key, no indexing facility
- Not a filesystem, cannot be mounted natively on EC2
- Anti patterns:
  - Lots of small files
  - POSIX file system (use EFS instead), file locks
  - Search features, queries, rapidly changing 
  - Website with dynamic content
- You can transition objects between tiers (or delete) using S3 Lifecycle Policies

## S3 Replication
- Cross Region Replication (CRR)
- Same Region Replication (SRR)
- Combine with Lifecycle Policies
- Helpful for reduce latency, DR, Security

## S3 Event Notifications
- events -> SNS -> SQS
- events -> SQS -> Lambda
- events -> async invoke lambda -> DLQ SQS
- S3 event notifications typically deliver events in seconds but can sometimes take a min or longer
- if two writes are made to a single non-versioned object at the same time, it is possible that only a signle event notification will be sent
- if you want to ensure that an event notification is sent for every successful write, enable bucket versioning

## S3 CloudWatch Events
- By default, CloudTrail records S3 bucket-level API calls
- CloudTrail logs for object-level can be enabled

## S3 Performance
- S3 auto scales to high request rates, latency 100-200ms
- can achieve at least 3.5k put/copy/post/delete and 5.5k get/head request per sec per prefix in a bucket
- Multipart upload
- S3 transfer acceleration: upload to an AWS edge location which will forward data to S3
- use Byte-range fetches to speed up download

## S3 Select & Glacier Select
- retrieve less data using SQL by performing server side filtering

## S3 Solution Architecture
- CloudFront -> EC2 -> EBS
- CloudFront -> ALB -> ASG -> EFS
- CloudFront -> S3