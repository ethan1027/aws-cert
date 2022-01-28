
## DynamoDB
- NoSQL db, fully managed, massive scale (1 mil rps)
- Similar to Cassandra (can migrate to DynamoDB)
- No disk space to provision, max object size is 400 KB
- Capacity: provisioned or on-demand
- Supports CRUD
- Backups available, point in time recovery
- Integrated with IAM for security
- made of tables
- Each table has a primary key
- Each table can have an infinite number of items
- Each item has attributes
- Maximum size of an item is 400k

## DynamoDB Primary Keys
- Options 1: Parition key only (HASH)
  - must be unique for each item
  - must be diverse so that the data is distributed
- Options 2: Parition key + Sort Key
  - the combination must be unique
  - data is grouped by partiion key
  - sort key == range key

## DynamoDB Indexes
- Object = primary key + optional sort key + attributes
- Local Secondary Index
  - keep the same primary
  - select an alternative sort key
  - must be defined at table creation time
- Global Secondary Index
  - change the primary key and optional sort key
  - Can be defined after the table is created
  - You need an index if you want to query a specific attribute

## DynamoDB - Important Features
- DynamoDB Streams: react to changes in real time
- Global Tables (Cross-Region Replication): must enable DynamoDB Streams

## DynamoDB Accelerator - DAX
- Seamless cache for DynamoDB, no application re-write
- Solves the Hot Key problem (too many reads)
5 min TTL for cache by default
- Up to 10 nodes in the cluster
- Multi AZ (3 nodes min recommended for production)

## DAX vs ElastiCache
- Use DAX as default for retrieval/read or query/scan
- Use ElastiCache for transformed data or aggregation results

## ElasticSearch/OpenSearch
- Managed version of ElasticSearch
- Needs to run on servers (not serverlesss offering)
- Use cases:
  - Log Analytics
  - Realtime application monitoring
  - Security Analytics
  - Full text search
  - Clickstream analytics
  - Indexing

## ElasticSearch + Kibana + Logstash
- Dynamo -> Dynamo Stream -> Lambda -> ElasticSearch
- CloudWatch Logs -> Subscription Filter -> Lambda -> ElasticSearch

## RDS
- Engines: PostgresSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server
- in VPC: usually in private subnet, control network access using security groups (important when using lambda)
- RDS Events: get notified via SNS for events
- Backups automated with point-in-time recovery. Backups expire
- Storage by EBS (gp2 or io1), can increase volume size with autoscaling
- Multi-AZ: standby instance for failover in case of outage
- Read Replicas: Increase read throughput. Can be cross-region

## Aurora
- DB Engines: PostgreSQL-compatible & MySQL-compatible
- Storage: automatically grows up to 64 TB, 6 copies of data, multi-AZ
- Read Replicas: up to 15 RR, reader endpoint to access them all
- Cross Region RR: entire database is copied
- Load/Offload data directly from/to S3
- 6 copies of your data across 3 AZ:
  - 4 copies out of 6 needed for writes
  - 3 copies out of 6 needed for reads
  - self healing with peer-to-peer replication
- Master + up to 15 Aurora Read Replicas

## Global Aurora
- 1 primary read/write region
- up to 5 secondary read-only region. lag is less than 1 second

## Aurora Multi-Master
- Every node can do read and write