# Amazon EventBridge Targets<a name="eventbridge-targets"></a>

## <a name="targets-overview"></a>

A target is a resource that is invoked when a rule is triggered\. You can configure the following as targets for events:
+ Amazon EC2 instances
+ SSM Run Command
+ SSM Automation
+ AWS Lambda functions
+ Data streams in Kinesis Data Streams
+ Data delivery streams in Kinesis Data Firehose
+ Amazon ECS tasks
+ AWS Step Functions state machines
+ AWS Batch jobs
+ CodeBuild projects
+ Pipelines in CodePipeline
+ Amazon Inspector assessment templates
+ Amazon SNS topics
+ Amazon SQS queues, including FIFO queues
+ The default event bus of another AWS account
+ Amazon API Gateway REST API endpoints
+ Amazon Redshift clusters \(Data API statement execution\)

EventBridge also includes some built\-in targets\. You can create rules for these built\-in targets only in the AWS Management Console\. The built\-in targets are `EC2 CreateSnapshot API call` , `EC2 RebootInstances API call`, `EC2 StopInstances API call`, and `EC2 TerminateInstances API call`\.

## Target\-specific Parameters<a name="targets-specific-parms"></a>

Some target types accept parameters\. For example, if you are using a Kinesis data stream target, you can optionally specify which shard the event goes to by using the `KinesisParameters` argument\. To invoke a command on multiple EC2 instances with one rule, you can use the `RunCommandParameters` field\. To pass data to API Gateway endpoints, you can use the `HttpParameters` field, including optional dynamic JSON path syntax \(see [API Gateway Target](targets-apigateway.md)\)\.

## Permissions<a name="targets-permissions"></a>

In order to make API calls against the resources that you own, EventBridge needs appropriate permissions\. For AWS Lambda and Amazon SNS resources, EventBridge relies on resource\-based policies\. For EC2 instances, Kinesis Data Streams, and AWS Step Functions state machines, EventBridge relies on IAM roles that you specify in the `RoleARN` argument in `PutTargets`\. API Gateway REST endpoints with configured IAM authorization can be invoked via IAM roles, but the role is optional if no Authorization is configured\. For more information, see [Identity and Access Management in Amazon EventBridge](auth-and-access-control-eventbridge.md)\.

 If another AWS account is in the same Region and has granted you permission \(using `PutPermission`\), you can send events to that account\. Set that account's event bus as a target of the rules in your account\. To send the matched events to the other account, specify that account's event bus as the `Arn` value when you run `PutTargets`\. If your account sends events to another account, your account is charged for each sent event\. Each event sent to another account is charged as a custom event\. The account receiving the event is not charged\. For more information, see [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing/)\.

**Note**  
 `Input`, `InputPath`, and `InputTransformer` are not available with `PutTarget` if the target is an event bus of a different AWS account\.

 If you are setting the event bus of another account as the target, and that account granted permission to your account through an organization instead of directly by the account ID, you must specify a `RoleArn` with proper permissions in the `Target` structure\. For more information, see [Sending and Receiving Events Between AWS Accounts](eventbridge-cross-account-event-delivery.md)\. For more information about enabling cross\-account events, see [PutPermission](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutPermission.html) in the *Amazon EventBridge API Reference*\.

 `Input`, `InputPath`, and `InputTransformer` are mutually exclusive and optional parameters of a target\. When a rule is triggered due to a matched event, the behavior is as follows: 
+  If none of the following arguments are specified for a target, the entire event is passed to the target in JSON format\. However, if the target is Amazon EC2 `Run` command or Amazon ECS task, nothing from the event is passed to the target\. 
+ If `Input` is specified in the form of valid JSON, the matched event is overridden with this constant\.
+ If `InputPath` is specified in the form of JSONPath \(for example, `$.detail`\), only the part of the event specified in the path is passed to the target \(for example, only the detail part of the event is passed\)\.
+ If `InputTransformer` is specified, one or more specified JSONPaths are extracted from the event and used as values in a template that you specify as the input to the target\.

When you specify `InputPath` or `InputTransformer`, you must use JSON dot notation, not bracket notation\.

When you add targets to a rule and the associated rule triggers soon after, new or updated targets might not be immediately invoked\. Allow a short period of time for changes to take effect\.

This action can partially fail if too many requests are made at the same time\. If that happens, `FailedEntryCount` is non\-zero in the response and each entry in `FailedEntries` provides the ID of the failed target and the error code\.