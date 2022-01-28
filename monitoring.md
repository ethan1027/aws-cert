## CloudWatch Alarm Integration
- CW Alarm -> CW Event -> Kinesis/Step Function/Lambda

## CloudWatch Events
- Intercept events from AWS services
- EC2 Instance Start, CodeBuild failure, S3, Trusted Advisor
- Can intercept any API call with Cloudtrail integration
- Targets: Lambda, Batch, ECS Task, Step Functions, CodePipeline, CodeBuild, SqS, SNS, Kinesis, etc.

## CloudWatch Logs
- Log groups: representing an application
- Log streams: instances with application
- Can define log expiration policies

## CloudWatch Logs Metric Filter & Insights
- find a specific IP insdie of a log
- count the occurences of Error in logs
- metric filters can be used to trigger alarms

## CloudWatch Logs - S3 Export
- S3 buckets must be encrypted with AES-256, not SSE-KMS
- Log data can take up to 12 hours to become available for export
- The API call is CreateExportTask

## CloudWatch Log Subscription
- CW Logs -> Subscription Filter -> Lambda/Firehose -> ES/S3

## X-Ray
- Tracing requests across your microservices
- Integrations with:
  - EC2 - install the X-Ray agent
  - ECs - install the X-Ray agent or Docker container
  - Lambda
  - Beanstalk - auto installed
  - API Gateway