# Events from AWS services<a name="eb-service-event"></a>

Many AWS services generate [events](eb-events.md) that EventBridge receives\. When an AWS service in your account emits an event, it goes to your account’s default event bus\.

 AWS CloudTrail is a service that automatically records events such as AWS API calls\. You can create EventBridge rules that use the information from CloudTrail\. For more information about CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)\.

All events that are delivered by CloudTrail have `AWS API Call via CloudTrail` as the value for `detail-type`\. Events from API actions that start with the keywords `List`, `Get`, or `Describe` aren't processed by EventBridge, with the exception of events from the following AWS STS actions:
+ `GetFederationToken`
+ `GetSessionToken`

To record events with a `detail-type` value of `AWS API Call via CloudTrail`, a CloudTrail trail with logging enabled is required\.

When using CloudTrail with Amazon S3, you need to configure CloudTrail to log data events\. For more information, see [Enabling CloudTrail event logging for S3 buckets and objects ](https://docs.aws.amazon.com/AmazonS3/latest/dev/enable-cloudtrail-logging-for-s3.html)\.

Some occurrences in AWS services can be reported to EventBridge both by the service itself and by CloudTrail\. For example, an Amazon EC2 API call that starts or stops an instance generates EventBridge events as well as events through CloudTrail\. 

**Important**  
CloudTrail is extending event delivery through EventBridge to resource owners\. Currently, CloudTrail supports both API callers and resource owners to receive events in their S3 buckets by creating trails, and delivers events to API callers through EventBridge\. With this change, resource owners in addition to API callers will be able to monitor cross\-account API calls through EventBridge\. CloudTrail’s integration with EventBridge provides a convenient way to set automated rules\-based workflows in response to events\.  
The CloudTrail team began deploying this update on April 12, 2021 and expect to complete this update in all commercial regions early this month\. As a result, some customers might experience an increase in the EventBridge events being delivered through EventBridge coinciding with this update\. No customer action is required, but should you have any questions, please reach out to AWS Support\. 

You can't use AWS Put\*Events API call events that are larger than 256 KB in size as event patterns because the maximum size of any Put\*Events requests is 256 KB\. For more information about the API calls that you can use, see [CloudTrail supported services and integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html)\.

**Note**  
Each AWS service that generates events sends them to EventBridge as either *best effort* or *guaranteed* delivery\. *Best effort delivery* means that the service attempts to send all events to EventBridge, but in some rare cases an event might not be delivered\. *Guaranteed delivery* means that all events from the service are successfully delivered to EventBridge\.  
This table includes a representation of the AWS services that send events to EventBridge, but it doesn't include every service\.

The following table shows AWS services that generate events\. Choose the service name to see more information about how that service and EventBridge work together\.


| Service | Delivery type | 
| --- | --- | 
| Amazon AppFlow | Best effort | 
| [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/monitoring-eventbridge.html) | Best effort | 
| [AWS Application Cost Profiler](https://docs.aws.amazon.com/application-cost-profiler/latest/userguide/monitoring-events.html) | Best effort | 
| Amazon Athena | Best effort | 
| [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/eventbridge.html) | Best effort | 
| [AWS Batch](https://docs.aws.amazon.com/batch/latest/userguide/batch_cwe_events.html) | Guaranteed | 
| [Amazon Braket](https://docs.aws.amazon.com/braket/latest/developerguide/braket-monitor-eventbridge.html) | Guaranteed | 
| AWS Certificate Manager Private Certificate Authority | Best effort | 
| [Amazon Chime](https://docs.aws.amazon.com/chime/latest/ag/automating-chime-with-cloudwatch-events.html) | Best effort | 
| [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks-event-bridge.html) | Best effort | 
| Events from AWS CloudTrail | Best effort | 
| [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-and-eventbridge.html) | Guaranteed | 
| AWS CodeArtifact | Guaranteed | 
| [AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-build-notifications.html#sample-build-notifications-ref) | Best effort | 
| [AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/monitoring-events.html) | Best effort | 
| [AWS CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/monitoring-cloudwatch-events.html) | Best effort | 
| [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/security-logging-and-monitoring.html#monitor-config-with-cloudwatchevents) | Best effort | 
| Amazon Connect | Best effort | 
| [AWS Control Tower](https://docs.aws.amazon.com/controltower/latest/userguide/monitoring-overview.html) | Best effort | 
| AWS Data Exchange | Best effort | 
| Amazon Data Lifecycle Manager | Best effort | 
| AWS DataSync | Best effort | 
| [Amazon DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-eventbridge.html) | Best effort | 
| [AWS Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/AWSHowTo.eventbridge.html) | Best effort | 
| [Amazon Elastic Block Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-cloud-watch-events.html) | Best effort | 
| Amazon Elastic Block Store volume modifications | Best effort | 
| Amazon Elastic Compute Cloud \(Amazon EC2\) | Best effort | 
| [Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/cloud-watch-events.html) | Best effort | 
| Amazon EC2 Fleets | Best effort | 
| [Amazon EC2 Spot Instance Interruption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html#spot-instance-termination-notices) | Best effort | 
| [Amazon Elastic Container Registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ecr-eventbridge.html#ecr-eventbridge-bus)  | Best effort | 
| [Amazon Elastic Container Service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch_event_stream.html) | Guaranteed | 
| [AWS Elemental MediaConvert](https://docs.aws.amazon.com/mediaconvert/latest/ug/cloudwatch_events.html) | Guaranteed | 
| AWS Elemental MediaLive | Best effort | 
| [AWS Elemental MediaPackage](https://docs.aws.amazon.com/mediapackage/latest/ug/monitoring-cloudwatch-events.html) | Best effort | 
| [AWS Elemental MediaStore](https://docs.aws.amazon.com/mediastore/latest/ug/monitoring-automating-with-cloudwatch-events.html) | Guaranteed | 
| Amazon EMR | Best effort | 
| [Amazon EventBridge scheduled rules](eb-create-rule-schedule.md) | Guaranteed | 
| Amazon GameLift | Best effort | 
| AWS Glue | Best effort | 
| AWS Glue DataBrew | Best effort | 
| [AWS Ground Station](https://docs.aws.amazon.com/ground-station/latest/ug/automating-events.html) | Best effort | 
| [AWS Health](https://docs.aws.amazon.com/health/latest/ug/cloudwatch-events-health.html) | Best effort | 
| [Amazon Inspector](https://docs.aws.amazon.com/inspector/latest/user/findings-managing-automating-responses.html) | Best effort | 
| [Amazon Interactive Video Service](https://docs.aws.amazon.com/ivs/latest/userguide/SUE.html) | Best effort | 
| AWS IoT Analytics | Guaranteed | 
| [AWS IoT Greengrass V1](https://docs.aws.amazon.com/greengrass/v1/developerguide/deployment-notifications.html) | Best effort | 
| [AWS IoT Greengrass V2](https://docs.aws.amazon.com/greengrass/v2/developerguide/telemetry.html) | Best effort | 
| AWS Key Management Service CMK deletion | Guaranteed | 
| AWS Key Management Service CMK rotation | Best effort | 
| AWS Key Management Service imported key material expiration | Best effort | 
| [Amazon Location Service](https://docs.aws.amazon.com/location/latest/developerguide/location-events.html) | Guaranteed | 
| [Amazon Macie](https://docs.aws.amazon.com/macie/latest/user/findings-publish-event-schemas.html) | Best effort | 
| Amazon Managed Blockchain | Best effort | 
| AWS Managed Services | Best effort | 
| AWS OpsWorks | Guaranteed | 
| [AWS Proton](https://docs.aws.amazon.com/proton/latest/adminguide/monitoring.html) | Best effort | 
| Amazon QLDB | Guaranteed | 
| [Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-cloudwatch-events.sample.html) | Best effort | 
| Amazon Redshift | Guaranteed | 
| AWS Resource Access Manager | Best effort | 
| [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/automating-savingsplans-with-eventbridge.html) | Best effort | 
| [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cloudwatch-events.html) | Guaranteed | 
| AWS Signer | Guaranteed | 
| [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/EventBridge.html) | Guaranteed | 
| Amazon Simple Workflow Service | Best effort | 
| [AWS Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/cw-events.html#cw-events-events) | Best effort | 
| AWS Storage Gateway | Guaranteed | 
| [AWS Support](https://docs.aws.amazon.com/awssupport/latest/user/event-bridge-support.html) | Best effort | 
| [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-eventbridge-events.html) | Best effort | 
| Tag changes on resources | Best effort | 
| AWS Transit Gateway | Best effort | 
| [Amazon Translate](https://docs.aws.amazon.com/translate/latest/dg/monitoring-with-eventbridge.html) | Guaranteed | 
| [AWS Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/cloudwatch-events-ta.html) | Best effort | 
| [Amazon WorkSpaces](https://docs.aws.amazon.com/workspaces/latest/adminguide/cloudwatch-events.html) | Best effort  | 