# AWS CLI Cheatsheet

## Table of Contents
- [General AWS CLI Information](#general-aws-cli-information)
- [EC2 (Elastic Compute Cloud)](#ec2-elastic-compute-cloud)
- [S3 (Simple Storage Service)](#s3-simple-storage-service)
- [IAM (Identity and Access Management)](#iam-identity-and-access-management)
- [CloudWatch (Monitoring and Logging)](#cloudwatch-monitoring-and-logging)
- [CloudFormation (Infrastructure as Code)](#cloudformation-infrastructure-as-code)
- [Lambda (Serverless Functions)](#lambda-serverless-functions)
- [ECS (Elastic Container Service)](#ecs-elastic-container-service)
- [RDS (Relational Database Service)](#rds-relational-database-service)
- [Networking (VPC, Route53, and ELB)](#networking-vpc-route53-and-elb)
- [Systems Manager (SSM)](#systems-manager-ssm)
- [Step Functions (Serverless Orchestration)](#step-functions-serverless-orchestration)
- [Advanced EC2 Management](#advanced-ec2-instance-management)
- [Autoscaling](#autoscaling-scaling-your-infrastructure)
- [Miscellaneous Useful AWS Commands](#miscellaneous-useful-aws-commands)

---

## General AWS CLI Information
- **`aws configure`**: Configures AWS CLI with your AWS credentials (Access Key, Secret Key, Region).
  - Example: `aws configure`
  - Note: This is the first step in using the AWS CLI. Make sure to use IAM roles and policies for secure access.

## EC2 (Elastic Compute Cloud)
- **`aws ec2 describe-instances`**: Lists all running EC2 instances.
  - Example: `aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId'`
  - Note: Use this command to filter by various instance properties using the `--filter` option.

- **`aws ec2 start-instances`**: Starts a stopped EC2 instance.
  - Example: `aws ec2 start-instances --instance-ids i-1234567890abcdef0`

- **`aws ec2 stop-instances`**: Stops a running EC2 instance.
  - Example: `aws ec2 stop-instances --instance-ids i-1234567890abcdef0`

- **`aws ec2 reboot-instances`**: Reboots a running instance.
  - Example: `aws ec2 reboot-instances --instance-ids i-1234567890abcdef0`

- **`aws ec2 terminate-instances`**: Terminates an EC2 instance (deletes the instance).
  - Example: `aws ec2 terminate-instances --instance-ids i-1234567890abcdef0`

- **`aws ec2 create-image`**: Creates an Amazon Machine Image (AMI) from an EC2 instance.
  - Example: `aws ec2 create-image --instance-id i-1234567890abcdef0 --name "MyServerBackup"`

## S3 (Simple Storage Service)
- **`aws s3 ls`**: Lists all S3 buckets or the contents of a specific bucket.
  - Example: `aws s3 ls` (lists all buckets)
  - Example: `aws s3 ls s3://my-bucket-name/` (lists contents of the specified bucket)

- **`aws s3 cp`**: Copies files to/from S3.
  - Example: `aws s3 cp myfile.txt s3://my-bucket-name/`
  - Example: `aws s3 cp s3://my-bucket-name/myfile.txt ./` (download a file)
  - Note: You can use `--recursive` to copy entire directories.

- **`aws s3 sync`**: Syncs files and directories between local storage and S3.
  - Example: `aws s3 sync ./my-folder s3://my-bucket-name/`
  - Note: Very efficient for backups or large data migrations.

- **`aws s3 rm`**: Deletes a file or folder from an S3 bucket.
  - Example: `aws s3 rm s3://my-bucket-name/myfile.txt`
  - Example: `aws s3 rm s3://my-bucket-name/ --recursive` (delete all files in the bucket)

- **`aws s3 presign`**: Generates a pre-signed URL for an S3 object.
  - Example: `aws s3 presign s3://my-bucket/my-object --expires-in 300`
  - Note: Useful for giving temporary access to S3 objects.

- **`aws s3api put-bucket-encryption`**: Enables server-side encryption for an S3 bucket.
  - Example: 
    ```bash
    aws s3api put-bucket-encryption --bucket my-bucket --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
    ```

## IAM (Identity and Access Management)
- **`aws iam create-user`**: Creates a new IAM user.
  - Example: `aws iam create-user --user-name newuser`

- **`aws iam list-users`**: Lists all IAM users in your account.
  - Example: `aws iam list-users`

- **`aws iam attach-user-policy`**: Attaches a managed policy to a user.
  - Example: `aws iam attach-user-policy --user-name newuser --policy-arn arn:aws:iam::aws:policy/AdministratorAccess`

- **`aws iam detach-user-policy`**: Detaches a managed policy from a user.
  - Example: `aws iam detach-user-policy --user-name newuser --policy-arn arn:aws:iam::aws:policy/AdministratorAccess`

- **`aws iam create-access-key`**: Creates access keys for a user (use with caution).
  - Example: `aws iam create-access-key --user-name newuser`

- **`aws iam delete-access-key`**: Deletes a user's access key.
  - Example: `aws iam delete-access-key --access-key-id AKIAIOSFODNN7EXAMPLE --user-name newuser`

## CloudWatch (Monitoring and Logging)
- **`aws cloudwatch describe-alarms`**: Lists all CloudWatch alarms.
  - Example: `aws cloudwatch describe-alarms`

- **`aws cloudwatch put-metric-alarm`**: Creates or updates a CloudWatch alarm.
  - Example: 
    ```bash
    aws cloudwatch put-metric-alarm --alarm-name "CPU_Utilization_Alarm" --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 70 --comparison-operator GreaterThanThreshold --evaluation-periods 2 --alarm-actions arn:aws:sns:region:account-id:sns-topic
    ```
  - Note: Use alarms to monitor key metrics and set up automated responses.

- **`aws cloudwatch get-metric-data`**: Retrieves historical CloudWatch metric data.
  - Example: 
    ```bash
    aws cloudwatch get-metric-data --metric-data-queries 'MetricDataQueries' --start-time 2024-01-01T00:00:00 --end-time 2024-01-02T00:00:00
    ```

## CloudFormation (Infrastructure as Code)
- **`aws cloudformation create-stack`**: Creates a new CloudFormation stack.
  - Example: `aws cloudformation create-stack --stack-name my-stack --template-body file://template.json`

- **`aws cloudformation update-stack`**: Updates an existing CloudFormation stack.
  - Example: `aws cloudformation update-stack --stack-name my-stack --template-body file://updated-template.json`

- **`aws cloudformation delete-stack`**: Deletes a CloudFormation stack.
  - Example: `aws cloudformation delete-stack --stack-name my-stack`

## Lambda (Serverless Functions)
- **`aws lambda list-functions`**: Lists all Lambda functions.
  - Example: `aws lambda list-functions`

- **`aws lambda create-function`**: Creates a new Lambda function.
  - Example: 
    ```bash
    aws lambda create-function --function-name my-function --zip-file fileb://function.zip --handler index.handler --runtime nodejs14.x --role arn:aws:iam::123456789012:role/execution_role
    ```

- **`aws lambda invoke`**: Invokes a Lambda function.
  - Example: `aws lambda invoke --function-name my-function outputfile.txt`

- **`aws lambda delete-function`**: Deletes a Lambda function.
  - Example: `aws lambda delete-function --function-name my-function`

## ECS (Elastic Container Service)
- **`aws ecs list-clusters`**: Lists all ECS clusters.
  - Example: `aws ecs list-clusters`

- **`aws ecs create-cluster`**: Creates a new ECS cluster.
  - Example: `aws ecs create-cluster --cluster-name my-cluster`

- **`aws ecs list-tasks`**: Lists tasks within a specified cluster.
  - Example: `aws ecs list-tasks --cluster my-cluster`

- **`aws ecs describe-tasks`**: Describes one or more tasks in a cluster.
  - Example: `aws ecs describe-tasks --cluster my-cluster --tasks task-id`

## RDS (Relational Database Service)
- **`aws rds describe-db-instances`**: Lists all RDS instances.
  - Example: `aws rds describe-db-instances`

- **`aws rds create-db-instance`**: Creates a new RDS instance.
  - Example: 
    ```bash
    aws rds create-db-instance --db-instance-identifier mydbinstance --db-instance-class db.t2.micro --engine mysql --allocated-storage 20 --master-username admin --master-user-password password
    ```

- **`aws rds delete-db-instance`**: Deletes an RDS instance.
  - Example: `aws rds delete-db-instance --db-instance-identifier mydbinstance --skip-final-snapshot`

## Networking (VPC, Route53, and ELB)

### VPC (Virtual Private Cloud)
- **`aws ec2 describe-vpcs`**: Lists all VPCs in your AWS account.
  - Example: `aws ec2 describe-vpcs`
  
- **`aws ec2 create-vpc`**: Creates a new VPC.
  - Example: `aws ec2 create-vpc --cidr-block 10.0.0.0/16`
  
- **`aws ec2 delete-vpc`**: Deletes a specified VPC.
  - Example: `aws ec2 delete-vpc --vpc-id vpc-12345678`

- **`aws ec2 describe-subnets`**: Lists all subnets in a specific VPC.
  - Example: `aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-12345678"`

### Route53 (DNS)
- **`aws route53 list-hosted-zones`**: Lists all hosted zones in your AWS account.
  - Example: `aws route53 list-hosted-zones`
  
- **`aws route53 create-hosted-zone`**: Creates a new hosted zone.
  - Example: `aws route53 create-hosted-zone --name example.com --caller-reference unique-string`
  
- **`aws route53 delete-hosted-zone`**: Deletes a hosted zone.
  - Example: `aws route53 delete-hosted-zone --id Z3M3LMPEXAMPLE`

### ELB (Elastic Load Balancing)
- **`aws elb describe-load-balancers`**: Lists all load balancers.
  - Example: `aws elb describe-load-balancers`
  
- **`aws elb create-load-balancer`**: Creates a new load balancer.
  - Example: `aws elb create-load-balancer --load-balancer-name my-lb --listeners Protocol=HTTP,LoadBalancerPort=80,InstanceProtocol=HTTP,InstancePort=80 --availability-zones us-east-1a`
  
- **`aws elb delete-load-balancer`**: Deletes a load balancer.
  - Example: `aws elb delete-load-balancer --load-balancer-name my-lb`

## Systems Manager (SSM)
- **`aws ssm send-command`**: Sends a command to one or more instances.
  - Example: 
    ```bash
    aws ssm send-command --instance-ids "i-1234567890abcdef0" --document-name "AWS-RunShellScript" --comment "IP config" --parameters commands=ifconfig
    ```
  - Note: This allows you to run commands remotely on EC2 instances without SSH access.

- **`aws ssm describe-instance-information`**: Lists all instances that have SSM installed and running.
  - Example: `aws ssm describe-instance-information`

- **`aws ssm get-parameter`**: Retrieves the value of a parameter stored in SSM.
  - Example: `aws ssm get-parameter --name "my-parameter-name"`

## Step Functions (Serverless Orchestration)
- **`aws stepfunctions list-state-machines`**: Lists all state machines.
  - Example: `aws stepfunctions list-state-machines`
  
- **`aws stepfunctions start-execution`**: Starts a new execution of a state machine.
  - Example: `aws stepfunctions start-execution --state-machine-arn arn:aws:states:us-east-1:123456789012:stateMachine:my-state-machine`
  
- **`aws stepfunctions describe-execution`**: Describes a running or completed execution.
  - Example: `aws stepfunctions describe-execution --execution-arn arn:aws:states:us-east-1:123456789012:execution:my-state-machine:execution-name`

## Advanced EC2 Instance Management
- **`aws ec2 create-snapshot`**: Creates a snapshot of an EBS volume.
  - Example: `aws ec2 create-snapshot --volume-id vol-12345678 --description "My backup snapshot"`
  
- **`aws ec2 modify-instance-attribute`**: Modifies the attribute of an instance (e.g., instance type, termination protection).
  - Example: `aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --instance-type "t2.micro"`
  
- **`aws ec2 describe-spot-instance-requests`**: Lists all Spot Instance requests.
  - Example: `aws ec2 describe-spot-instance-requests`

## Autoscaling (Scaling Your Infrastructure)
- **`aws autoscaling describe-auto-scaling-groups`**: Lists all Auto Scaling groups.
  - Example: `aws autoscaling describe-auto-scaling-groups`
  
- **`aws autoscaling create-auto-scaling-group`**: Creates a new Auto Scaling group.
  - Example: 
    ```bash
    aws autoscaling create-auto-scaling-group --auto-scaling-group-name my-asg --launch-configuration-name my-launch-config --min-size 1 --max-size 3 --desired-capacity 2 --vpc-zone-identifier subnet-12345678
    ```

- **`aws autoscaling update-auto-scaling-group`**: Updates the settings of an Auto Scaling group.
  - Example: 
    ```bash
    aws autoscaling update-auto-scaling-group --auto-scaling-group-name my-asg --min-size 2 --max-size 5
    ```

## Miscellaneous Useful AWS Commands
- **`aws sts get-caller-identity`**: Displays the AWS account and IAM role details associated with the current credentials.
  - Example: `aws sts get-caller-identity`

- **`aws ec2 describe-regions`**: Lists all AWS regions.
  - Example: `aws ec2 describe-regions`

- **`aws configure list`**: Displays the current CLI configuration settings (including credentials).
  - Example: `aws configure list`
