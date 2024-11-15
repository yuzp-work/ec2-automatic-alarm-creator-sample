# EC2 Automatic Alarm Creator

=============================Disclaimer========================================

The sample code or script or other assets are provided as Service Content (in the case that you are using Amazon Web Services China regions) or AWS Content (in the case that you are using Amazon Web Services regions outside Mainland China) (“Assets”) under the Customer Agreement or the relevant written agreement between you and the operator of the relevant Amazon Web Services region (whichever applies). You should not use the Assets in your production accounts, or on production or other critical data. You are responsible for testing, securing, and optimizing the Assets, as appropriate for production grade use based on your specific quality control practices and standards.  Deploying the Assets may incur charges for creating or using Amazon Web Services chargeable resources, such as running Amazon EC2 instances or using Amazon S3 storage.
示例代码或脚本或其他资料（“资料”）作为您与相关亚马逊云科技区域运营商之间的客户协议或相关书面协议（以适用者为准）下的“服务内容”（如果您使用亚马逊云科技中国区域）或“AWS 内容”（如果您使用中国大陆以外的亚马逊云科技区域）而提供给您。您不应在您的生产账户中使用“资料”，也不应将“资料”用于您的生产或其他关键数据。您负责根据您的特定质量控制实践和标准对“资料”进行测试、采取安全措施和优化，以适合生产级用途。部署“资料”可能会因创建或使用亚马逊云科技收费资源（例如运行Amazon EC2 实例或使用 Amazon S3 存储）而产生费用。

=============================End of disclaimer ===================================

This CDK project implements automated EC2 instance monitoring with the following features:

- Automatically installs CloudWatch Agent on newly created EC2 instances
- Creates CloudWatch alarms for CPU and Memory utilization
- Sends email notifications when thresholds are exceeded
- Automatically cleans up alarms when instances are terminated

## Architecture

The solution includes:

- EventBridge rule to monitor EC2 instance state changes
- Lambda function to handle instance creation/termination events
- Systems Manager automation for CloudWatch Agent installation
- CloudWatch alarms for monitoring metrics
- SNS topic for email notifications
- IAM roles and policies for secure operation

## Prerequisites

1. AWS CDK CLI installed
2. Node.js 18.x or later
3. AWS CLI configured with appropriate credentials
4. An AWS account with sufficient permissions

## Alarm Thresholds

- CPU Utilization: > 80% for 1 consecutive 1-minute periods
- Memory Usage: > 75% for 1 consecutive 1-minute periods
> You can modify these thresholds or add more alarms in `src/lambda/ec2-monitor.ts`.

## Deployment Instructions

1. Clone the repository and navigate to the project directory:
   ```bash
   cd ec2-automatic-alarm-creator
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Deploy the stack with your notification email:
   ```bash
   cdk deploy -c email=your-email@example.com
   ```

4. Confirm the SNS topic subscription in your email

## Using with EC2 Instances

When launching new EC2 instances, make sure to:

1. Use the Instance Profile ARN output from the stack deployment
2. The instance must have internet connectivity to communicate with Systems Manager
3. Ensure the instance has the SSM Agent installed (comes pre-installed on Amazon Linux 2)

## Cleanup

To remove all resources:

```bash
cdk destroy
```

## Stack Outputs

The stack provides two important outputs:

1. `EC2InstanceProfileARN` - Use this when launching EC2 instances
2. `SNSTopicARN` - The ARN of the SNS topic where alerts are sent

## Monitoring Details

The stack monitors the following:

- EC2 instance state changes (running/terminated)
- CPU utilization (threshold: 80%)
- Memory utilization (threshold: 75%)

When an instance enters the 'running' state:
1. CloudWatch Agent is automatically installed via Systems Manager
2. CPU and Memory alarms are created

When an instance is terminated:
- All associated alarms are automatically cleaned up

## Troubleshooting

1. Check CloudWatch Logs for Lambda function execution logs
2. Verify Systems Manager Run Command history for CloudWatch Agent installation
3. Ensure EC2 instances have proper IAM roles and internet connectivity
4. Confirm SNS topic subscription is confirmed

## Security

The solution implements least-privilege security principles:
- Lambda functions have minimal required permissions
- EC2 instances use instance profiles with specific permissions
- All resources use AWS-managed policies where appropriate

## Command Line Parameters

The stack requires one parameter during deployment:

- `email`: The email address where monitoring notifications will be sent
  ```bash
  cdk deploy -c email=your-email@example.com
  ```

If you try to deploy without providing an email address, the deployment will fail with an error message asking you to provide one.
