## Cost Allocation Tags
- With Tags we can track resources that relate to each other
- With Cost Allocation Tags we can enable detailed costing reports
- Just like Tags, but they show up as columns in Reports
- AWS Generated Cost Allocation Tags:
  - Auto applied to the resource you create
  - prefix aws
  - They are not applied to resources created before the activation
- User Tags:
  - prefix user
  - takes up to 24 hours to show up in the report

# Trusted Advisor
- analyse your AWS accounts and provides recommendation:
  - cost optimization rec
  - performance
  - security
  - fault tolerance
  - service limits
- Core checks and rec - all customers
- can enable weekly email notification from the console
- Full Trusted Advisor - Available for Business & Enterprise support plans
  - Ability to set CloudWatch alarms when reaching limits
  - Programmatic access using AWS Support API
- Service Limits
  - limits can only be monitored
  - Cases have to be created manually in AWS Support Centre
  - Use the new AWS Service Quotas service

## EC2 Instance Launch Types
- On Demand Instances: short workload, predictable pricing, reliable
- Spot Instances: short workloads, for cheap, can lose instances (not reliable)
- Reserved: (minimum 1 year)
  - reserved instances: long workloads
  - Convertible reserved instances: long workloads with flexible instances
  - Scheduled reserved instances: example - every Thursday between 3 and 6PM
- Dedicated Instances: no other customers will share your hardware
- Dedicated Hosts: book an entire physical server, control instance placement
  - Great for software license that operate at the core, or socket level
  - Can define host affinity so that instance reboots are kept on the same host

## AWS Savings Plan
- New pricing model to get a discount based on long-term usage
- Commit to a dollar amount
- EC2 Instance Savings plan (up to 72%)
  - select instance family and locked to a specific region
  - flexible across size, OS, tenancy
- Compute Savings plan (up to 66%):
  - Ability to move between instance family, region, compute type, OS, and tenancy

## S3 Storage Classes
- Standard - General Puurpose
- Standard - Infrequent Access
- One Zone - Infrequent Access
- Intelligent Tiering
- Glacier
- Glacier Deep Archive
- S3 Select & Glacier Select: save in network and CPU cost
- S3 Lifecycle Rules: transition objects between tiers
- Compress objects to save space
- S3 Requester Pays

## S3 Standard - Infrequent Access
- Suitable for data that is less frequently accessed but requires rapid access when needed
- High durability (99.9%) of objects across multiple AZs
- 99.9% availability
- Low cost compared to S3 Standard
- Use cases: data store for DR, backups

## S3 One Zone - Infrequent Access
- Same as IA but data is stored in a single AZ
- 99.5 availability
- Low latency and high throughput performance
- Supports SSL for data at transit

## S3 Intelligent Tiering
- Same low latency and high throughput as S3 Standard
- Small monthly monitoring and auto-tiering fee
- Auto moves objects between 2 tiers
- 99.9% durability across multi AZ

## Glacier
- Low cost object storage meant for archiving/backup