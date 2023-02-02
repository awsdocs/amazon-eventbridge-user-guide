# Amazon EventBridge Pipes targets<a name="eb-pipes-event-target"></a>

You can send data in your pipe to a specific target\. You can configure the following targets when setting up a pipe in EventBridge:
+ [API destination](eb-api-destinations.md)
+ [API Gateway](eb-api-gateway-target.md)
+ [Batch job queue](#pipes-targets-specifics-batch)
+ [CloudWatch log group](#pipes-targets-specifics-cwl)
+ [ECS task](#pipes-targets-specifics-ecs-task)
+ Event bus in the same account and Region
+ Firehose delivery stream
+ Inspector assessment template
+ Kinesis stream
+ Lambda function \(SYNC or ASYNC\)
+ Redshift cluster data API queries
+ SageMaker Pipeline
+ SNS topic
+ SQS queue
+ Step Functions state machine
  + Express workflows \(SYNC or ASYNC\)
  + Standard workflows \(ASYNC\)

## Target parameters<a name="pipes-targets-specific-parms"></a>

Some target services don't send the event payload to the target, instead, they treat the event as a trigger for invoking a specific API\. EventBridge uses the [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html) to specify what information gets sent to that API\. These include the following:
+ API destinations \(The data sent to an API destination must match the structure of the API\. You must use the [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate) object to make sure the data is structured correctly\. If you want to include the original event payload, reference it in the [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate)\.\)
+ API Gateway \(The data sent to API Gateway must match the structure of the API\. You must use the [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate) object to make sure the data is structured correctly\. If you want to include the original event payload, reference it in the [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetParameters.html#pipes-Type-PipeTargetParameters-InputTemplate)\.\)
+ [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetRedshiftDataParameters.html](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetRedshiftDataParameters.html) \(Amazon Redshift Data API clusters\)
+ [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetSageMakerPipelineParameters.html](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetSageMakerPipelineParameters.html) \(Amazon SageMaker Model Building Pipelines\)
+ [https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetBatchJobParameters.html](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_PipeTargetBatchJobParameters.html) \(AWS Batch\)

### Dynamic path parameters<a name="pipes-targets-dynamic-parms"></a>

EventBridge Pipes target parameters support optional dynamic JSON path syntax\. You can use this syntax to specify JSON paths instead of static values \(for example `$.detail.state`\)\. The entire value has to be a JSON path, not only part of it\. For example, `RedshiftParameters.Sql` can be `$.detail.state` but it can't be `"SELECT * FROM $.detail.state"`\. These paths are replaced dynamically at runtime with data from the event payload itself at the specified path\. Dynamic path parameters can't reference new or transformed values resulting from input transformation\. The supported syntax for dynamic parameter JSON paths is the same as when transforming input\. For more information, see [Amazon EventBridge Pipes input transformation](eb-pipes-input-transformation.md)\.

Dynamic syntax can be used on all string, non\-enum fields of all EventBridge Pipes enrichment and target parameters except `HttpParameters.HeaderParameters` keys\.

## Permissions<a name="pipes-targets-permissions"></a>

To make API calls on the resources that you own, EventBridge needs appropriate permission\. EventBridge uses the IAM role that you specify on the pipe for enrichment and target calls\.

## EventBridge Pipes target specifics<a name="pipes-targets-specifics"></a>

### AWS Batch job queues<a name="pipes-targets-specifics-batch"></a>

All AWS Batch `submitJob` parameters are configured explicitly with `BatchParameters`, and as with all Pipe parameters, these can be dynamic using a JSON path to your incoming event payload\.

### CloudWatch Logs group<a name="pipes-targets-specifics-cwl"></a>

Whether you use an input transformer or not, the event payload is used as the log message\. You can set the `Timestamp` \(or the explicit `LogStreamName` of your destination\) through `CloudWatchLogsParameters` in `PipeTarget`\. As with all pipe parameters, these parameters can be dynamic when using a JSON path to your incoming event payload\.

### Amazon ECS task<a name="pipes-targets-specifics-ecs-task"></a>

All Amazon ECS `runTask` parameters are configured explicitly through `EcsParameters`\. As with all pipe parameters, these parameters can be dynamic when using a JSON path to your incoming event payload\.