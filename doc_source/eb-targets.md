# Amazon EventBridge targets<a name="eb-targets"></a>

A *target* is a resource or endpoint that EventBridge sends an [event](eb-events.md) to when the event matches the event pattern defined for a [rule](eb-rules.md)\. **The rule processes the [event](eb-events.md) data and sends the pertinent information to the target\. To deliver event data to a target, EventBridge needs permission to access the target resource\. You can define up to five targets for each rule\.

When you add targets to a rule and that rule runs soon after, any new or updated targets might not be immediately invoked\. Allow a short period of time for changes to take effect\.



## Targets available in the EventBridge console<a name="eb-console-targets"></a>

You can configure the following targets for events in the EventBridge console:
+ [API destination](eb-api-destinations.md)
+ [API Gateway](eb-api-gateway-target.md)
+ [Batch job queue](#targets-specifics-batch)
+ [CloudWatch log group](#targets-specifics-cwl)
+ [CodeBuild project](#targets-specifics-codebuild)
+ CodePipeline
+ Amazon EBS `CreateSnapshot` API call
+ EC2 Image Builder
+ EC2 `RebootInstances` API call
+ EC2 `StopInstances` API call
+ EC2 `TerminateInstances` API call
+ [ECS task](#targets-specifics-ecs-task)
+ [Event bus in a different account or Region](eb-cross-account.md)
+ [Event bus in the same account and Region](eb-bus-to-bus.md)
+ Firehose delivery stream
+ Glue workflow
+ [Incident Manager response plan](https://docs.aws.amazon.com//incident-manager/latest/userguide/incident-creation.html#incident-tracking-auto-eventbridge)
+ Inspector assessment template
+ Kinesis stream
+ Lambda function \(ASYNC\)
+ Redshift cluster data API queries
+ SageMaker Pipeline
+ SNS topic
+ SQS queue
+ Step Functions state machine \(ASYNC\)
+ Systems Manager Automation
+ Systems Manager OpsItem
+ Systems Manager Run Command

## Target parameters<a name="targets-specific-parms"></a>

Some targets don't send the information in the event payload to the target, instead, they treat the event as a trigger for invoking a specific API\. EventBridge uses the [Target](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_Target.html) parameters to determine what happens with that target\. These include the following:
+ API destinations \(The data sent to an API destination must match the structure of the API\. You must use the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html) object to make sure the data is structured correctly\. If you want to include the original event payload, reference it in the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html)\.\)
+ API Gateway \(The data sent to API Gateway must match the structure of the API\. You must use the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html) object to make sure the data is structured correctly\. If you want to include the original event payload, reference it in the [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_InputTransformer.html)\.\)
+ Amazon EC2 Image Builder
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RedshiftDataParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RedshiftDataParameters.html) \(Amazon Redshift Data API clusters\)
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_SageMakerPipelineParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_SageMakerPipelineParameters.html) \(Amazon SageMaker Model Building Pipelines\)

### Dynamic path parameters<a name="targets-dynamic-parms"></a>

Some target parameters support optional dynamic JSON path syntax\. This syntax allows you to specify JSON paths instead of static values \(for example `$.detail.state`\)\. The entire value has to be a JSON path, not just part of it\. For example, `RedshiftParameters.Sql` can be `$.detail.state` but it can't be `"SELECT * FROM $.detail.state"`\. These paths are replaced dynamically at runtime with data from the event payload itself at the specified path\. Dynamic path parameters can't reference new or transformed values resulting from input transformation\. The supported syntax for dynamic parameter JSON paths is the same as when transforming input\. For more information, see [Amazon EventBridge input transformation](eb-transform-target-input.md)

Dynamic syntax can be used on all the string, non\-enum fields of these parameters:
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_EcsParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_EcsParameters.html)
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_HttpParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_HttpParameters.html) \(except `HeaderParameters` keys\)
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RedshiftDataParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RedshiftDataParameters.html)
+ [https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_SageMakerPipelineParameters.html](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_SageMakerPipelineParameters.html)

## Permissions<a name="targets-permissions"></a>

To make API calls on the resources that you own, EventBridge needs appropriate permission\. For AWS Lambda and Amazon SNS resources, EventBridge uses [resource\-based policies](eb-use-resource-based.md)\. For EC2 instances, Kinesis data streams, and Step Functions state machines, EventBridge uses IAM roles that you specify in the `RoleARN` parameter in `PutTargets`\. You can invoke an API Gateway endpoint with configured IAM authorization, but the role is optional if you haven't configured authorization\. For more information, see [Amazon EventBridge and AWS Identity and Access Management](eb-iam.md)\.

If another account is in the same Region and has granted you permission, then you can send events to that account\. For more information, see [Sending and receiving Amazon EventBridge events between AWS accounts](eb-cross-account.md)\.



If your target is encrypted, you must include the following section in your KMS key policy\.

```
{
    "Sid": "Allow EventBridge to use the key",
    "Effect": "Allow",
    "Principal": {
        "Service": "events.amazonaws.com"
    },
    "Action": [
        "kms:Decrypt",
        "kms:GenerateDataKey"
    ],
    "Resource": "*"
}
```

## EventBridge target specifics<a name="targets-specifics"></a>

### AWS Batch job queues<a name="targets-specifics-batch"></a>

Certain parameters to AWS Batch `submitJob` can be configured via [BatchParameters](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_BatchParameters.html)\.

Others can be specified in the event payload\. If the event payload \(passed through or via [InputTransformers](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html)\) contains the following keys, they are mapped to `submitJob` [request parameters](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html#API_SubmitJob_RequestSyntax):
+ `ContainerOverrides: containerOverrides`
**Note**  
This includes only command, environment, memory, and vcpus
+ `DependsOn: dependsOn`
**Note**  
This includes only jobId
+ `Parameters: parameters`

### CloudWatch Logs group<a name="targets-specifics-cwl"></a>

If you don’t use an [InputTransformer](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html) with a CloudWatch Logs target, the event payload is used as the log message, and the source of the event as the timestamp\. If you do use an InputTransformer, the template must be:

`{"timestamp":<timestamp>,"message":<message>}`

### CodeBuild project<a name="targets-specifics-codebuild"></a>

If you use [InputTransformers](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html) to shape the input event to a Target to match the CodeBuild [StartBuildRequest](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_StartBuild.html#API_StartBuild_RequestSyntax) structure, the parameters will be mapped 1\-to\-1 and passed through to `codeBuild.StartBuild`\.

### Amazon ECS task<a name="targets-specifics-ecs-task"></a>

If you use [InputTransformers](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-transform-target-input.html) to shape the input event to a Target to match the Amazon ECS RunTask [TaskOverride](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_TaskOverride.html) structure, the parameters will be mapped 1\-to\-1 and passed through to `ecs.RunTask`\.

### Incident Manager Response Plan<a name="targets-specifics-incident-manager"></a>

If the matched event came from CloudWatch Alarms, the alarm state change details are populated into the trigger details of the StartIncidentRequest call to Incident Manager\.