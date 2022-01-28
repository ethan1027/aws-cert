## Auto Scaling
- Spot fleet support (mix on spot and On-Demand instances)
- to upgrade an AMI, must update the launch configuration/template
  - You must terminate instances manually
  - Cloudformation can help with that step
- Scheduled scaling actions:
  - Modify the ASG settings (min/max/desired) at pre-defined time
  - Helpful when patterns are known in advance
- Lifecycle Hooks:
 - Perform actions before an instance is in service, or before it is terminated
 - Exmples: cleanup, log extraction, special health checks

## Scaling processes
- Launch: Add a new EC2 to the group, increasing the capacity
- Terminate: Removes an EC2 instance from the group, decreasing its capacity
- HealthCheck: Checks the health of the instances
- ReplaceUnhealthy: Terminate unhealthy instances and re-create them
- AZRebalance: Balancer the number of EC2 instances across AZ
- AlarmNotification: Accept notification from Cloudwatch
- ScheduledActions: Performs scheduled actions that you create
- AddtoLoadBalancer: Adds instances to the load balancer or target group

## Health Checks:
- EC2 Status Checks
- ELB Health Checks
- ASG will launch a new instance after terminating an unhealthy one
- Make sure the health check is simple and checks the correct thing

## Auto Scaling - Solution Architecture
- Same target group, same ALB
- Split traffic between target group, same ALB
- Use Route 53 CNAME Weighted Record to use Client based LB to send traffic to 2 ALBs and 2 target groups

## Spot Fleets
- Collection of spot instances and optionally on-demand instances
- Set a max price to pay per spot instances or all
- Can have a mix of instance types (m5.large, m5.xlarge, etc)
- Supports: EC2 standalone, auto scaling groups, ECS (ASG), AWS Batch
- Soft Limits:
  - Target capacity per Spot Fleet or EC2 fleet: 10k
  - Target capacity across all Spot fleet and EC2 Fleet in a region: 100k

## EC2 Spot Instances
- Can get a discount of up to 90% compared to On-demand
- Define max spot price and get the instance while current spot price < max
  - The hourly spot price varies based on offer and capacity
  - If the current spot price > your max price, you can choose to stop or terminate your instance with a 2 minutes grace period
- other strategy: Spot Block
  - block spot instance during a specified time frame without interruptions
  - In rare situations, the instance may be reclaimed
- Use for batch jobs, data nalysis, or workloads that are resilient to failures
- Not great for critical jobs or databases


## AWS ECS - Elastic Container Service
- ECS is a container orchestration service
- ECS helps you run Docker containers on EC2 machines
- ECS is complicated, and made of:
  - "ECS Core": Running ECS on user-provisioned EC2 instances
  - Fargate: Running ECS tasks on AWS-provisioned compute (serverless)
  - EKS: Running ECS on AWS-powered Kubernetes (running on EC2)
  - ECR: Docker Container Registry hosted by AWS
## ECS Use Cases:
- Run microservices
- Run batch processing/scheduled tasks

## ECS Concepts:
- ECS Cluster: set of EC2 instances
- ECS Service: applications definitions running running on ECS cluster
- ECS Tasks + Definition: Containers running to create the application
- ECS IAM Roles: roles assigned to tasks to interact with AWS

## AWS ECS - ALB Integration
- ALB has a direct integration feature with ECS called "port mapping"
- This allows you to run multiple instances of the same application on the same EC2 machine
- Use cases:
  - Increased resiliency even if running on one EC2 instance
  - Maximimze utilization of CPU/cores
  - Ability to perform rolling upgrades without impacting application uptime

## Fargate
- When launching an ECs Cluster, we have to create our EC2 instances
- If we need to scale, we need to add EC2 instances
- With Fargate, we don't provision Ec2 instances
- We just create task definitions

## Security & Networking
- IAM Security
  - EC2 Instance Role must have basic ECS permissions
  - ECS Task level should have an IAM Task Role (maximum security)
- Secrets and Configuration injection into parameters, environment variables:
  - Integration with SSM parameter Store & Secrets Manager
- Tasks networking:
  - none: no network connectivity, no port mapping
  - bridge: uses Docker's virtual container-based network
  - host: bypass Docker's network, uses the underlying host network interface
  - awsvpc:
    - Every tasks laucned on the instance gets its own ENi and a private IP address
    - Simplified networking, enhanced security, security groups, monitoring, VPC flow logs
    - Default for Fargate


## AWS Lambda Integrations
- API Gateway
- Kinesis
- DynamoDB
- S3
- IoT
- Cloudwatch Events
- Cloudwatch Logs
- SNS
- Cognito
- SQS

## Lambda Limits
- RAM: 128MB to #G
- CPU: linked to RAM
- Timeout: 15 mins
- tmp storage: 512 (can't process big files)
- Deployment package limit: 250 MB including layers
- Concurrency execution: 1000 - soft limit that can be increased

## Lambda Latencies Considerations
- Lambda Latency:
  - Cold lambda invocation: ~100ms
  - Warm lambda invocation: ~ms
  - "povisioned concurrency" reduce cold starts
- API Gateway invocation: 100ms
- CloudFront invocation: 100ms
- X-Ray can help visualize the end-to-end latency

## Lambda Security
- Resource-based Policies for Lambda: Allow other accounts/services to invoke and manage lambda

## Lambda - Event Source Mapping
- Kinesis Data Streams, SQS, SQS FIFO queue, DynamoDB Streams
- Destination
  - Asynchronous invocations: can define destinations for successful and failed event: SQS, SNS, Lambda, EventBridge

## Lambda Aliases
- Aliases are "pointers" to lambda function versions
- we can define dev, test, prod aliases and have them point at different lambda versions
- Aliases are mutable
- Aliases enable Blue/Green deployment by assigning weights to lambda functions

## Lambda Traffic Shifting
- Linear: grow traffic every N minutes until 100%
- Canary: try X percent for N minutes then 100%
- AllAtOnce: immediate
- Can create Pre & Post traffic hooks to check the health of the lambda function

## Load Balancer
- Classic Load Balancer (v1 - old generation) - 2009: http, https, tcp
- Application Load Balancer (v2- new gen) - 2016: http, https, websocket
- CLB supports only one SSL cert. SSL cert must be changed anytime SAN is added/removed
- Better use ALB with SNI if possible

## Application Load Balancer Target Groups:
- EC2 instances (can be managed by an ASG) - HTTP
- ECS tasks (managed by ECS itself) - HTTP
- Lambda functions - HTTP request is translated into a JSON event
- IP Addresses - must be private IPs (ex: instances in peered VPC, on-premise)
- ALB can route to multiple target groups
- Health checks are at target group level

## Network Load Balancer (v2)
- Forware TCP traffic to your instances (UDP support - Jun 2019)
- Handle millions of requests per seconds
- NLB has one static IP per AZ, and supports assigning Elastic IP (helpful for whitelisting specific IP)
- NLB are mostly used for extreme performance, TCP or UDP with AWS Private Link to expose a service internally

## Network Load Balancer Target Groups:
- EC2 instances - TCP
- ECS tasks - TCP
- Ip addresses - private only

## Cross-Zone Load Balancing
- distribute evenly across all registered instances in all AZ
- CLB:
  - Disabled by default
  - No charges for inter AZ data if enabled
- ALB:
  - Always on (cannot be disabled)
  - No charges for inter AZ data
- NLB:
  - Disabled by default
  - You pay charges for inter AZ data

## Load Balancer Stickiness
- same client is always redirected to the same instance behind a load balancer
- works for CLB and ALB
- the "cookie" used for stickiness has an expiration date you control
- use cases: make sure the user doesn't lose his session data
- enabling stickiness may bring imbalance to the load over EC2 instances
- Alternative is to cache session data in ElastiCache or DynamoDB

## API Gateway
- Expose Lambda, HTTP & AWS Services as an API
- API versioning, authorization, traffic management (API keys, throttles), huge scale, serverless, req/resp transformations, OpenAPI spec, CORS
- Limits: 29 seconds timeout, 10mb max payload size
- API changes are deployed to "Stages" (as many as you want)
- Use the naming you like for stages
- Stages can be rolled back as a history of deployments is kept

## API Gateway Integrations
- HTTP
- Lambda
- AWS Service:
  - API Gateway in front of S3: impacted by 10MB payload size limit
  - Better architecture: API Gateway -> invoke lambda -> generate pre-signed URL -> forward URL -> upload files of any size to S3 using the pre-signed URL

## API Gateway - Endpoint types
- Edge-Optimized (default): for global clients
  - Requests are routed through the CloudFront Edge locations (improves latency)
  - The API Gateway still lives in only one region
- Regional:
  - Could manually combine with CloudFront for more control over the caching strategies
- Private
  - Can only accessed from your VPC using an interface VPC endpoint (ENI)
  - Use a resource policy to define access

## Caching API Responses
- Default TTL is 300 seconds (5 min)
- Caches are defined per stage
- Possible to override cache settings per method

## Route 53
- Managed DNS
- A: hostname to IPv4
- AAAA: hostname to IPv6
- CNAME: hostname to hostname
- Alias: hostname to AWS resource: CLB, ALB, NLB, CloudFront, S3, Elastic Beanstalk
- DNS Records TTL:
  - high TTL: 24 hours - less traffice on DNS, possibly outdated records
  - low TTL: 30s - more traffic on DNS, records are outdated for less time, easy to change records
  - TTL is mandatory for DNS records

## Simple Routing Policy
- Maps a hostname to a single resource
- You can't attach health checks to simple routing policy

## Weighted Routing Policy
- Control the % of the requests that go to specific endpoint
- Helpful to test 1% of traffic on new app version for example
- Helpful to split traffic between two regions - load balancing
- Can be associated with health checks

## Latency Routing Policy
- Redirect to the server that has the least latency close to us
- Super helpful when latency of users is a priority
- Latency is evaluated in terms of user to designated AWS region
- Has a failover capability if you enable health checks

## Geo Location Routing Policy
- Different from latency based
- This is routing based on user location
- Here we specify: traffic from teh UK should go to this specific IP

## Route 53 Complex/Nested Records

## Multi Value Routing Policy
- Use when roting traffic to multiple resources
- Want to associate a Route 53 health checks with records
- Up to 8 healthy records are returned for each Multi Value query
- Multi Value is not a substitute for having an ELB

## Route 53 - good to know
- Private DNS:
  - Can use Route 53 for internal private DNS
  - Must enable the VPC settings enableDnsHostNames and enableDnsSupport
- NDSSEC (protect against Man in the middle attack)
  - Route 53 supports DNSSEC for domain registration
  - Route 53 supports DNSSEC for DNS service as of Dec 2020 (using KMS)
  - Run a custom DNS server on Amazon EC2 (Bind is most popular)
- 3rd party registrar:
  - Buy domain out of AWS and use Route53 as your DNS provider
  - Update the NS records on the 3rd party registrar

## Health Checks with Route 53
- Health check means automated DNS failovers
- Route 53 health checkers are outside the VPC
- They can't access private endpoints
- To check resource within a VPC, you must assign a public IP address
- You can configure the health checker to check the health of an external resource the instance relies on
- You can create aCloudWatch metric and associate an alarm, You then create a health check that checks the alarm

## Health Checks Solution Architecture RDS multi-region failover
- RDS Main us-east-1 -> CW Alarm/healh-check route -> Http call health check -> CW Alarm Bridge linked to Health check -> CW Event linked to CW Alarm (or SNS topic) -> Lambda -> Update DNs -> Promotte Read Replicas

# Route 53 Solution architecture Sharing a Private Zone across VPC
- Having a central private Shared Services DNS
- VPC Peering
- Use AWS CLI only

## Solution Architecture Comparisons
- Ec2 on its own with Elastic IP
- EC2 with Route53
- ALB + ASG: 
  - Scales well, classic architecture
  - users are not sent to instances that are out-of-service
  - time to scale is slow (EC2 instance bootstrap - AMI can help)
  - Could lose a few requests if instances are overloaded
  - ALB is elastic but can't handle sudden huge peak of demand (pre-warm)
  - Cross-zone balancing for traffic distribution
  - Target utilization should be between 40% and 70%
- ALB + ECS on EC2:
  - Sam properties as ALB + ASG
  - Application on Docker
  - Allow to have dynamic port mappings
- ALB + ECS on Fargate:
  - Service Auto Scaling is easy
  - time to be in-service is quick (no need to launch an Ec2 instance in advance)
  - still limited by the ALB in case of sudden peaks
  - "serverless" application tier
  - "managed" load balancer
- ALB + Lambda:
  - Limited to Lambda's runtimes
  - Seamless scaling thanks to lambda
  - simple way to expose lambda function as http/s without all the features from API Gateway
  - Can combine with Web Application Firewall
  - good for hybrid microservices
- API Gateway + Lambda:
  - Soft limits: 10k/s requests, 1000 concurrent lambda
  - rate limiting, caching, authentication
- API Gateway + AWS Service:
  - Better architecture: Client -> API Gateway -> SQS
- API Gateway + HTTP backend