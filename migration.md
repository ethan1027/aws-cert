## Cloud Migration: The 6R
- Rehosting: lift and shift
  - Simple migrations by re-hosting on AWS (applications, databses, data, ...)
  - No cloud optimizations being done, application is migrated as is
  - Could save as much as 30% on cost
  - Migrate using AWS VM Import/Export, AWS Server Migration Service
- Replatforming:
  - Migrate your database to RDS
  - Migrate your application to Elastic Beanstalk
  - Not changing the core architecture, but leverage some cloud optimizations
- Repuchase: drop and shop
  - Moving to a different product while moving to the cloud
  - Often you move to a SaaS platform
  - Expensive in short term but quick to deploy
  - CRM to Salesforce, HR to Workday, CMS to Drupal
- Refactoring/Re-architecting
  - Reimagining how the application is architected using Cloud Native features
  - Driven by the need of the business to add features, scale, performance
  - move an application to Serverless architecture, use AWS S3
- Retire
- Retain

## AWS Storage Gateway
- Bridge between on-prem data and cloud data in S3
- DR, backup & restore, tired storage
- 3 types of Storage Gateway:
  - File Gateway
  - Volume Gateway
  - Tape Gateway

## File Gateway
- File Gateway appliance is a virtual machine to bridge between your NFS and S3
- Configured S3 buckets are accessible using the NFS and SMB protocol
- Bucket access using IAM roles for each File Gateway
- Each file Gateway should have an IAM role to access S3
- Most recently used data is cached in File Gateway
- can be mounted on many servers
- Read Only replicas
- Define S3 Lifecycle Policies from S3 Standard -> S3 Standard IA -> S3 Glacier over time
- Enable S3 Object Versioning
- RefreshCache API on the Gateway to be notified of restore
- S3 Object Lock
  - Enable to have the File Gateway for Write Once Read Many (WORM) data
  - If there are file modifications or renames in the file share clients, the file gateway creates a new version oft he object without affecting prior versions, and the original locked version will remain unchanged

## Volume Gateway
- block storage using iSCSI protocol backed by S3
- Cached volumes: low latency access to most recent data, full data on S3
- Stored volumes: entire data set is on-prem, shceduled backups to S3
- Can create EBS snapshots from the volumes and restore asa EBS
- Files in volume are not directly accessible in S3
- Up to 32 volumes per gateway:
  - Each volume up to 32TB in cached mode (1PB per Gateway)
  - Each volume up to 16TB in stored mode (512TB per Gateway)
- Application Server -> iSCSI -> Volume -> HTTPS -> Storage Gateway S3 -> EBS snapshots

## Tape Gateway
- Some companies have backup processes using physical tapes
- Let companies use the same process but in cloud
- Virtual Tape Library backed by S3 and Glacier
- iSCSI interface
- Can't access single file within tapes. Need to resotre the tape entirely in S3/Glacier

## AWS Snow Family
- Highly secure, portable devices to collect and process data at the edge and migrate data into and out of AWS
- Data Migration: Snowcone, Snowball Edge, Snowmobile
- Edge computing: Snowcone, Snowball Edge
- Time to Transfer: 100Mbps, 1Gps, 10Gps
- Offline devices to peerform data migration
- If it takes more than a week to transfer over the network, use snowball devices

## AWS Snowcone
- Small, portable computing, anywhere, withstands harsh environments
- 8Tb of usable storage

## AWS Snowmobile
- Transfer Exabytes of data
- Each Snowmobile has 100PB of capacity

## DMS - Database Migration Service
- Quickly and securely migrate db to AWS

## DMS Sources and Targets
- Source:
  - On-prem and EC2 instance databases
  - Azure SQL DB
  - RDS
  - S3
- Target:
  - On-prem and EC2 instance databses
  - RDS
  - Redshift
  - DynamoDB
  - S3
  - ElasticSearch
  - Kinesis
  - DocumentDB