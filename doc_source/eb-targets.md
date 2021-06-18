# Amazon EventBridge targets<a name="eb-targets"></a>

A *target* is a resource or endpoint that EventBridge sends an [event](eb-events.md) to when the event matches the event pattern defined for a [rule](eb-rules.md)\. **The rule processes the [event](eb-events.md) data and sends the pertinent information to the target\. To deliver event data to a target, EventBridge needs permission to access the target resource\. You can define up to five targets for each rule\.

When you add targets to a rule and that rule runs soon after, any new or updated targets might not be immediately invoked\. Allow a short period of time for changes to take effect\.

## Targets available in the EventBridge console<a name="eb-console-targets"></a>

You can configure the following targets for events in the EventBridge console:
+ [API destination](eb-api-destinations.md)
+ [API Gateway](eb-api-gateway-target.md)
+ AWS Batch job queue
+ Amazon CloudWatch Logs group
+ AWS CodeBuild project
+ AWS CodePipeline
+ Amazon Elastic Compute Cloud \(Amazon EC2\) `CreateSnapshot` API call
+ Amazon EC2 `RebootInstances` API call
+ Amazon EC2 `StopInstances` API call
+ Amazon EC2 `TerminateInstances` API call
+ Amazon ECS task
+ [Event bus in a different AWS account or AWS Region](eb-cross-account.md)
+ Firehose delivery stream \(Amazon Kinesis Data Firehose\)
+ Inspector assessment template \(Amazon Kinesis Data Streams\)
+ Kinesis stream \(Kinesis Data Streams\)
+ AWS Lambda function
+ Amazon Redshift cluster \(Data API statement execution\)
+ SageMaker Pipeline
+ Amazon SNS topic
+ Amazon SQS queue \(including a FIFO queue\)
+ Amazon EC2 Systems Manager \(SSM\) Automation
+ SSM OpsItem
+ SSM Run Command
+ AWS Step Functions state machine

## Target parameters<a name="targets-specific-parms"></a>

These targets accept parameters\. 
+ For a Kinesis data stream target, you can specify which shard the event goes to by using the `KinesisParameters` parameter\. 
+ To invoke commands on multiple Amazon EC2 instances with one rule, you can use the `RunCommandParameters` field\. 
+ To pass data to an API Gateway endpoint, you can use the `HttpParameters` field, including optional dynamic JSON path syntax\. Any `HttpParameters` field value \(except `HeaderParameters` map keys\) can contain JSON paths instead of static values \(for example `$.detail.state`\)\. Such paths are replaced dynamically at runtime with data from the event payload itself at the specified path\. The supported syntax for dynamic parameter JSON paths is the same as for Target InputTransformers\. For more information, see [Transforming Amazon EventBridge target input](eb-transform-target-input.md)\.
+ To pass data to an Amazon Redshift cluster, you can use the `RedshiftDataParameters` field, including optional dynamic JSON path syntax\.
+ To pass data to an SageMaker pipeline, you can use the `SageMakerPipelineParameters` field, including optional dynamic JSON path syntax\.

`Input`, `InputPath`, and `InputTransformer` are the mutually exclusive and optional parameters of a target\. When a rule runs, the behavior is as follows: 
+  If you don't specify any parameters for the target, then the entire event is passed to the target in JSON format\. However, if the target is an Amazon EC2 `Run` command or an Amazon ECS task, the event isn't passed to the target\. 
+ If you specify `Input` in the form of valid JSON, then the matched event is overridden with this JSON\.
+ If you specify `InputPath` in the form of JSONPath, then only the part of the event that's specified in the path is passed to the target\. For example, if you specify `$.detail`, then only the detail part of the event is passed\.
+ If you specify `InputTransformer`, then one or more specified JSONPaths are extracted from the event and used as values in a template that you specify as the input to the target\.

When you specify `InputPath` or `InputTransformer`, you must use JSON dot notation, not bracket notation\.

## Permissions<a name="targets-permissions"></a>

To make API calls on the resources that you own, EventBridge needs appropriate permission\. For AWS Lambda and Amazon SNS resources, EventBridge uses [resource\-based policies](eb-use-resource-based.md)\. For EC2 instances, Kinesis data streams, and Step Functions state machines, EventBridge uses IAM roles that you specify in the `RoleARN` parameter in `PutTargets`\. You can invoke an API Gateway REST endpoint with configured IAM authorization, but the role is optional if you haven't configured authorization\. For more information, see [Amazon EventBridge and AWS Identity and Access Management](eb-iam.md)\.

If another account is in the same Region and has granted you permission, then you can send events to that account\. For more information, see [Sending and receiving Amazon EventBridge events between AWS accounts and Regions](eb-cross-account.md)\.

