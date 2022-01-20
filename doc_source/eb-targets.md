# Amazon EventBridge targets<a name="eb-targets"></a>

A *target* is a resource or endpoint that EventBridge sends an [event](eb-events.md) to when the event matches the event pattern defined for a [rule](eb-rules.md)\. **The rule processes the [event](eb-events.md) data and sends the pertinent information to the target\. To deliver event data to a target, EventBridge needs permission to access the target resource\. You can define up to five targets for each rule\.

When you add targets to a rule and that rule runs soon after, any new or updated targets might not be immediately invoked\. Allow a short period of time for changes to take effect\.



## Targets available in the EventBridge console<a name="eb-console-targets"></a>

You can configure the following targets for events in the EventBridge console:
+ [API destination](eb-api-destinations.md)
+ [API Gateway](eb-api-gateway-target.md)
+ Batch job queue
+ CloudWatch log group
+ CodeBuild project
+ CodePipeline
+ EC2 `CreateSnapshot` API call
+ EC2 Image Builder
+ EC2 `RebootInstances` API call
+ EC2 `StopInstances` API call
+ EC2 `TerminateInstances` API call
+ ECS task
+ [Event bus in a different account or Region](eb-cross-account.md)
+ [Event bus in the same account and Region](eb-bus-to-bus.md)
+ Firehose delivery stream
+ Glue workflow
+ [Incident Manager response plan](https://docs.aws.amazon.com//incident-manager/latest/userguide/incident-creation.html#incident-tracking-auto-eventbridge)
+ Inspector assessment template
+ Kinesis stream
+ Lambda function
+ Redshift cluster
+ SageMaker Pipeline
+ SNS topic
+ SQS queue
+ Step Functions state machine
+ Systems Manager Automation
+ Systems Manager OpsItem
+ Systems Manager Run Command

## Target parameters<a name="targets-specific-parms"></a>

When you configure targets, there are additional parameters you can specify for certain AWS services\. These include the following:
+ `BatchParameters` \(AWS Batch jobs\)
+ `EcsParameters` \(Amazon ECS tasks\)
+ `HttpParameters` \(Amazon API Gateway and 3rd party ApiDestination endpoints\)
+ `KinesisParameters` \(Amazon Kinesis streams\)
+ `RedshiftDataParameters` \(Amazon Redshift Data API clusters\)
+ `RunCommandParameters` \(Amazon EC2 Instance commands\)
+ `SageMakerPipelineParameters` \(Amazon SageMaker Model Building Pipelines\)
+ `SqsParameters` \(Amazon SQS queues\)

Some target parameters support optional dynamic JSON path syntax\. This syntax allows you to specify JSON paths instead of static values \(for example `$.detail.state`\)\. These paths are replaced dynamically at runtime with data from the event payload itself at the specified path\. The supported syntax for dynamic parameter JSON paths is the same as when transformoning input\. For more information, see [Transforming Amazon EventBridge target input](eb-transform-target-input.md)

Dynamic syntax can be used on all the non\-enum fields of these parameters:
+ `EcsParameters`
+ `HttpParameters` \(except `HeaderParameters`\)
+ `RedshiftDataParameters`
+ `SageMakerPipelineParameters`

## Permissions<a name="targets-permissions"></a>

To make API calls on the resources that you own, EventBridge needs appropriate permission\. For AWS Lambda and Amazon SNS resources, EventBridge uses [resource\-based policies](eb-use-resource-based.md)\. For EC2 instances, Kinesis data streams, and Step Functions state machines, EventBridge uses IAM roles that you specify in the `RoleARN` parameter in `PutTargets`\. You can invoke an API Gateway REST endpoint with configured IAM authorization, but the role is optional if you haven't configured authorization\. For more information, see [Amazon EventBridge and AWS Identity and Access Management](eb-iam.md)\.

If another account is in the same Region and has granted you permission, then you can send events to that account\. For more information, see [Sending and receiving Amazon EventBridge events between AWS accounts](eb-cross-account.md)\.

