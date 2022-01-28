
## Step Functions
- Build serverless visual workflow to orchestrate your lambda functions
- Represent flow as a JSON state machine
- Features: sequence, parallel, conditions, timeouts, error handling
- Can also integrate with EC2, ECS, on-prem servers, API Gateway
- Maximum execution time of 1 year

## Step Function Tasks
- Invoke a lambda function
- Activity Worker, EC2
- ECS task, Fargate, DynamoDB, SNS, Batch job, SQS
- Step function does not integrate natively with Mechanical Turk

## SWF - Simple Workflow Service (Older Service)

## SQS
- Serverless, managed queue, integrated with IAM
- Can handle extreme scale, no provisioning
- Used to decouple services
- Message size of max 256 KB
- Could be used as a write buffer for DynamoDB

## Amazon MQ
- Traditional applications running from on-prem may use open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
- Amazon MQ - managed Apache ActiveMQ
- Does not scale as much as SQS and SNS