# Events from AWS services<a name="eb-service-event"></a>

Many AWS services generate [events](eb-events.md) that EventBridge receives\. Each AWS service that generates events sends them to EventBridge as either *best effort* or *guaranteed* delivery\. *Best effort delivery* means that the service attempts to send all events to EventBridge, but in some rare cases an event might not be delivered\. *Guaranteed delivery* means that all events from the service are successfully delivered to EventBridge\.

When an AWS service in your account emits an event, it goes to your account’s default event bus\.

You can also use EventBridge with AWS services that don't emit events and aren't listed on this page\. AWS CloudTrail is a service that automatically records events such as AWS API calls\. You can create EventBridge rules that use the information from CloudTrail\. For more information about CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\. For information about creating an EventBridge rule that uses CloudTrail, see [Tutorial: Create an Amazon EventBridge rule for AWS CloudTrail API calls](eb-ct-api-tutorial.md)\.

All events that are delivered by CloudTrail have `AWS API Call via CloudTrail` as the value for `detail-type`\. 

Some occurrences in AWS services can be reported to EventBridge both by the service itself and by CloudTrail\. For example, an Amazon EC2 API call that starts or stops an instance generates EventBridge events as well as events through CloudTrail\. 

**Important**  
CloudTrail is extending event delivery through EventBridge to resource owners\. Currently, CloudTrail supports both API callers and resource owners to receive events in their S3 buckets by creating trails, and delivers events to API callers through EventBridge\. With this change, resource owners in addition to API callers will be able to monitor cross\-account API calls through EventBridge\. CloudTrail’s integration with EventBridge provides a convenient way to set automated rules\-based workflows in response to events\.  
The CloudTrail team began deploying this update on April 12, 2021 and expect to complete this update in all commercial regions early this month\. As a result, some customers might experience an increase in the EventBridge events being delivered through EventBridge coinciding with this update\. No customer action is required, but should you have any questions, please reach out to AWS Support\. 

You can't use AWS API call events that are larger than 256 KB in size as event patterns\. For more information about the API calls that you can use, see [Services Supported by CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-supported-services.html)\.

The following table shows AWS services that generate events\.


| Service | Delivery type | 
| --- | --- | 
| Amazon AppFlow | Best effort | 
| [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/monitoring-eventbridge.html) | Best effort | 
| Amazon Athena | Best effort | 
| [Amazon Augmented AI](https://docs.aws.amazon.com/sagemaker/latest/dg/augmented-ai-cloudwatch-events.html) |  | 
| [AWS Batch](https://docs.aws.amazon.com/batch/latest/userguide/batch_cwe_events.html) | Guaranteed | 
| [Amazon Braket](https://docs.aws.amazon.com/braket/latest/developerguide/braket-monitor-eventbridge.html) | Guaranteed | 
| AWS Certificate Manager Private Certificate Authority | Best effort | 
| [Amazon Chime](https://docs.aws.amazon.com/chime/latest/ag/automating-chime-with-cloudwatch-events.html) | Best effort | 
| AWS CloudTrail, events delivered by |  | 
| [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-eventbridge.html) | Guaranteed | 
| AWS CodeArtifact | Guaranteed | 
| [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-build-notifications.html#sample-build-notifications-ref) | Best effort | 
| [AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/monitoring-events.html) | Best effort | 
| [AWS CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/monitoring-cloudwatch-events.html) | Best effort | 
| AWS CodePipeline |  | 
| [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/monitor-config-with-cloudwatchevents.html) | Best effort | 
| Amazon Connect | Best effort | 
| AWS Data Exchange | Best effort | 
| Amazon Data Lifecycle Manager | Best effort | 
| AWS DataSync | Best effort | 
| [AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.eventbridge.html) | Best effort | 
| [Amazon Elastic Block Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-cloud-watch-events.html) | Best effort | 
| Amazon Elastic Block Store volume modifications | Best effort | 
| Amazon Elastic Compute Cloud \(Amazon EC2\) | Best effort | 
| [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/cloud-watch-events.html) | Best effort | 
| Amazon EC2 Fleets | Best effort | 
| [Amazon EC2 Instance Rebalance Recommendation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/rebalance-recommendations.html#monitor-rebalance-recommendations) |  | 
| [Amazon EC2 Spot Instance Interruption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html#spot-instance-termination-notices) | Best effort | 
| Amazon EC2 state change |  | 
| [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr-eventbridge.html#ecr-eventbridge-bus)  | Best effort | 
| [Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch_event_stream.html) | Guaranteed | 
| [AWS Elemental MediaConvert](https://docs.aws.amazon.com/mediaconvert/latest/ug/cloudwatch_events.html) | Guaranteed | 
| AWS Elemental MediaLive | Best effort | 
| [AWS Elemental MediaPackage](https://docs.aws.amazon.com/mediapackage/latest/ug/monitoring-cloudwatch-events.html) | Best effort | 
| [AWS Elemental MediaStore](https://docs.aws.amazon.com/mediastore/latest/ug/monitoring-automating-with-cloudwatch-events.html) | Guaranteed | 
| Amazon EMR | Best effort | 
| Amazon GameLift | Best effort | 
| AWS Glue | Best effort | 
| AWS Glue DataBrew | Best effort | 
| [AWS Ground Station](https://docs.aws.amazon.com/ground-station/latest/ug/automating-events.html) | Best effort | 
| [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings_cloudwatch.html) |  | 
| AWS Health | Best effort | 
| [Amazon Interactive Video Service](https://docs.aws.amazon.com/ivs/latest/userguide/SUE.html) | Best effort | 
| AWS IoT Analytics | Guaranteed | 
| [AWS IoT Greengrass V1](https://docs.aws.amazon.com/greengrass/v1/developerguide/deployment-notifications.html) | Best effort | 
| [AWS IoT Greengrass V2](https://docs.aws.amazon.com/greengrass/v2/developerguide/telemetry.html) | Best effort | 
| AWS Key Management Service CMK deletion | Guaranteed | 
| AWS Key Management Service CMK rotation | Best effort | 
| AWS Key Management Service imported key material expiration | Best effort | 
| [Amazon Location Service](https://docs.aws.amazon.com/location/latest/developerguide/location-events.html) | Guaranteed | 
| [Amazon Macie](https://docs.aws.amazon.com/macie/latest/user/findings-publish-event-schemas.html) | Best effort | 
| Amazon Macie Classic | Best effort | 
| Amazon Managed Blockchain | Best effort | 
| AWS Managed Services | Best effort | 
| AWS Management Console Sign\-in |  | 
| AWS OpsWorks | Guaranteed | 
| Amazon QLDB | Guaranteed | 
| Amazon Redshift | Best effort | 
| Amazon Relational Database Service | Best effort | 
| AWS Resource Access Manager | Best effort | 
| [Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/automating-sagemaker-with-eventbridge.html) |  | 
| [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/automating-savingsplans-with-eventbridge.html) | Best effort | 
| [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cloudwatch-events.html) | Best effort | 
| AWS Server Migration Service |  | 
| AWS Signer | Guaranteed | 
| Amazon Simple Workflow Service | Best effort | 
| [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/cw-events.html#cw-events-events) | Best effort | 
| AWS Storage Gateway | Guaranteed | 
| AWS Systems Manager |  | 
| Tag changes on resources | Best effort | 
| AWS Transit Gateway | Best effort | 
| AWS Trusted Advisor |  | 
| [Amazon Workspaces](https://docs.aws.amazon.com/workspaces/latest/adminguide/cloudwatch-events.html) | Best effort  | 
| AWS X\-Ray |  | 