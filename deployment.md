## Elastic Beanstalk
- developer centric view of deploying an application
- all in one view that's easy to make sense of
- Beanstalk is free but you have to pay for the underlying assets
- support: Go, Java SE, Java Tomcat, .Net, Node, PHP, Python, multicontainer Docker, preconfigured Docker
- instance configuration and OS are handled by Beanstalk
- Single instance is great for dev
- High availability with ALB is great for prod
- If your application performs tasks that are long to complete, offload these tasks to a dedicated worker environment
- decoupling application into 2 tiers is common
- define tasks in cron.yaml

## Elastic Beanstalk Blue/Green Deployment
- Not a "direct feature" of EB
- Zero downtime and release facility
- Create anew stage environment and deploy v2 there
- The new environment (green) can be validated independently and roll back if needed
- Route 53 can be setup using weighted policies to redirect a bit of traffic to the stage environment

## OpsWorks
- Chef & Puppet help you perform server configuration automatically, or repetitive actions
- Work great with Ec2 & on-prem VM
- alternative to AWS SSM
- Migrating other tech stack to OpsWorks is not easy
- Help manage configuration as code
- works with Linux and Windows

## CodeDeploy to EC2
- In-place update to your fleet of EC2 instances
- Can use hooks to verify deployment after each deployment phase

## CodeDeploy to ASG
- In-place updates
- Blue/Green deployment
- must be using ELB

## CodeDeploy to AWS Lambda
- Traffic Shifting feature
- Pre and Post traffic hooks
- Easy & automated rollback using CloudWatch Alarms
- SAM framework natively uses CodeDeploy

## CodeDeploy to ECS
- Support Blue/Green Deployments for Amazon ECS and Fargate
- setup is done within the ECS service definition
- A new task set is created and traffic is re-routed to the new task set

## Retaining Data on Deletes
- Put a DeletionPolicy on any resource to control what happens when the CloudFormation template is deleted
- DeletionPolicy=Retain:
  - Specify on resources to preserve/back in case of CF deletes
- DeletionPolicy=Snapshot:
  - EBSVolume, ElastiCache, RDS, Redshift
- DeletePolicy=Delete (default):
  - the default policy for RDS is Snapshot
  - To delete an S3 bucket, you need to first empty the bucket

## CloudFormation and IAM
- uses the permission of our own IAM principal
- or assign an IAM role to the stack that can perform the actions

## CloudFormation - Cross vs Nested Stacks
- Cross Stacks
  - Helpful when stacks have different lifecycles
  - When you need to pass export values to many stacks (VPC ID)
- Nested Stacks
  - Helpful when components must be re-used
  - re-use how to preperly configure an ALB
  - The nested stack only is important to the higher level stack
- Clouformer: Create an AWS CloudFormation template from existing AWS resources
- ChangeSets: Generate & Preview the CF changes before they get applied
- StackSets: Deploy CF stack across multiple accounts and regions
- Stack Policies: Prevent accidental updates/deletes to stack resources

## Service Catalog
- Teams are presented a self-service portal where they can launch the products
- Helps with governance, compliance, and consistency

## SAM - Serverless Application Model
- Lambda, DynamoDB, API Gateway , Cognito User Pools
- SAM can run Lambda, API Gateway, and DynamoDB locally
- SAM can use CodeDeploy to deploy lambda functions

## CodeDeploy (no new AMI - application deployments)
- In-place on EC2
- In-place on ASG
- New instances on ASG
- Traffic shifting for AWS Lambda
- New task set for ECS + traffic shifting

## AWS Systems Manager
- Helps you manage your EC2 and on-prem systems at scale
- Get operational insights about the state of your infrastructure
- Patching automation for enhanced compliance
- Works for both Windows And Linux OS
- Integrated with CloudWatch metrics/dashboards
- Integrated with AWS Config
- Free service
- Insights:
  - Insights dashboard
  - Inventory: discover and audit the software installed
  - compliance
- Action:
  - Automation (shut down EC2, create AMIS)
  - Run command
  - Session manager
  - Patch manager
  - Maintenance Windows

## How Systems Manager works
- We need to install the SSM agent onto the systems we control
- Installed by default on Amazon Linux AMI & some Ubuntu AMI
- If an instance can't be controlled with SSM, it's probalby an issue with the SSM agent
- Make sure the EC2 instances have a proper IAM role to allow SSM actions

## AWS Systems Manager Run Command
- Execute a document or one command
- Run command across multiple instances (using resource groups)
- Rate control and error control
- Integrated with IAM & CloudTrail
- No need for SSH (more secure)
- Results in the console
- SSM Agent talks to SSM Service

## SSM Patch Manager - Predefined Patch Baselines
- Defines which patches should or shouldn't be installed on your instances
- Linux:
  - AWS-AmazonLinux2DefaultPatchBaseline
  - AWS-CentOSDefaultPatchBaseline
  - AWS-RedHatDefaultPatchBaseline
  - AWS-SuseDefaultPatchBaseline
  - AWS-UbuntuDefaultPatchBaseline
- Windows: (patches are auto-approved 7 days after the release)
  - AWS-DefaultPatchBaseline: install OS patch CriticalUpdates & SecurityUpdates
  - AWS-WindowsPredefinedPatchBaseline-OS: same as DefaultPatch
  - AWS-WindowsPredefinedPatchBaseline-OS-Applications: also updates Microsoft applications
  - Can define your own custom patch baselines as well
## SSM Patch Managers - Steps
1. Define a patch baseline to use
2. Define patch groups - use Patch Group
3. Define Maintenance Windows
4. Add the Run Command as part of the registered tasks
5. Define Rate Control
6. Monitor Patch Compliance using SSM Inventory

## AWS Parameter Store
- Secure storage for configuration and secrets
- Optional seamless encryption using KMS
- serverless, scalable, durable, easy SDK, free
- version tracking configurations/secrets
- Configuration management using path & IAM
- Notifications with CloudWatch Events
- Integration with CF