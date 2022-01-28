## Kinesis
- managed data streaming service
- Great for application logs, metrics, IoT, clickstreams
- Great for real-time big data
- Great for streaming processing frameworks (Spark, etc)
- Data is auto replicated synchronously to 3 AZ
- Streams: low latency streaming ingest at scale
- Analytics: perform real-time analytics on streams using SQL
- Firehose: load streams into S3, Redshift, ElasticSearch, and Splunk

## Kinesis Streams
- Data retention is 24 hours by default, can go up to 7 days
- Ability to reprocess and replay data
- Once data is inserted in Kinesis, it can't be deleted until retention
- One stream is made of many different shards
- Billing is per shard provisioned, can have as many ashards as you want
- Records are ordered per shard

## Kinesis Producers:
- AWS SDK: simple producer
- Kinesis Producer Library (KPL): batch, compression, retries, C++, Java
- Kinesis Agent: can write to Streams and Firehose

## Kinesis Consumers
- AWS SDK: simple consumer
- Lambda (event source mapping)
- KCL: checkpointing coordinated reads

## Kinesis Limit
- Producer:
  - 1MB/s or 1k messages/s at write per shard
  - ProvisionedThroughputException otherwise
- Consumer Classic:
  - 2MB/s at read per shard across all consumers
  - 5 API calls per sec per shard across all consumers
- Consumer Enhanced Fan-out:
  - 2MB/s at read per shard, per enhanced consumer
  - no API calls needed

## Kinesis Data Firehose
- AWS Destinations: S3, Redshift, ElasticSearch
- 3rd Party Destinations: Splunk, New Relic, Datadog, MongoDB
- Custom Destinations: HTTP Endpoint
- Serverless
- Streams -> Firehose -> S3 -> Redshift

## Firehose Buffer Sizing
- Firehose accumulates records in a buffer
- The buffer is flushed based on time and size rules
- Buffer Size (ex: 32MB): if the bbuffer size is reached, it's flushed
- Buffer Time (1 min): if the time is reached, it's flushed
- Firehose can autom increase the buffer size to increase throughput
- If real-time flush from Streams to S3 is needed, use lambda

## Kinesis Data Analytics
- Use Cases:
  - Streaming ETL: select columns, make simple transformations on streaming data
  - Continuous Metric Generation: live leaderboard for a mobile game
  - Responsive analytics: look for a certain criteria and build alerting
- Lambda can be used for pre-processing
- Schema Discovery
- SQL or Flink to write the computation

## Streaming Architectures
- 3k messages of 1KB per second
- Kinesis:
  - 3 shards: 3MB/s in
  - 3 * 0.015/hr = $32.4/month
  - Must use KDF for output to S3
- DynamoDB + Streams
  - 3K WCU = 3MB/s
  - 1450/month
  - Storage in Dynamo

## AWS Batch
- run as Docker images
- Dynamic provisioning of the instances - in VPC
- serverless
- Orchestrate jobs using Step Functions
- Laucnh with within your VPC: use NAT gateway or VPC endpoints for ECS

## AWS Batch - Multi-Node Mode
- large scale, good for high performance computing
- works better if EC2 is in a placement group.

## EMR
- launched within a VPC in the same AZ
- EBS is HDFS
- native integration with S3 (EMRFS)
- launch Hive in EMR

## EMR - Node types and purchasing
- Master Node: manage the cluster, health
- Core Node: run tasks and store data
- Task Node: run tasks
- Purchasing options:
  - On-demand: reliable, predictable, won't be terminated
  - Reserved (min 1 year): cost saving
  - Spot Instance: cheaper, can be terminated

## Running Jobs
- Strat 1 : provision EC2 instance (long running - CRON jobs)
- Strat 2: Cloudwatch Events + Lambda (cron)
- Strat 3: S3, SQS, SNS + Lambda
- Strat 4: AWS Batch: CloudWatch Events -> Batch
- Strat 5: CloudWatch Events -> Fargate
- Strat 6: Use EMR (step execution or cluster)

## Redshift
- based on PostgreSQL, but not used for OLTP
- OLAP - online analytical processing
- 10x better performance than other data warehouses, scale to PBs of data
- Refshift is provisioned, so it's worth it when you have a sustained usage (use Athena if the queries are sporadic instead)

## Redshift - Snapshots and DR
- Snapshots are point-in-time backups of a cluster, stored internally in S3
- Snapshots are incremental
- You can restore a snapshot into a new cluster
- Automated: every 8 hours, every 5GB, or on schedule
- Manual: like RDS
- Can configure to auto cop snapshots to another region

## Redshift Spectrum
- Query data that is already in S3 without loading it
- Must have a Redshift cluster available to start the query

## Athena & Quicksight
- serverless SQL queries on top of your data in S3, pay per query, output to S3
- Queries are logged in CloudTrail
- Great for sporadic queries
- Queries are logged in CloudTrail
- Great for sporadic queries
- Ready-to-use queries for VPC Flow Logs, ClouTrail ALB Access Logs, Cost and Usage reports
- S3/Glue Data Catalog -> Athena -> S3
- RDS/S3, Redshift, Athena, Jira, Excel, etc -> Quicksight

## Comparison of warehousing technologies
- EMR
  - Need to use Big Data tools such as Hive, Spark
  - One long-running cluster, many jobs, with auto-scaling
  - Can access data in Dynamo or S3
  - Scratch data on EBS(HDFS) and S3(EMRFS)
- Athena
  - Simple queries and aggregations, data must live in S3
  - Serverless, simple SQL queries
  - Audit queries through CloudTrail
- Redshift
  - Advanced SQL queries, must provision server
  - Can leverage Redshift Spectrum for serverless queries on S3