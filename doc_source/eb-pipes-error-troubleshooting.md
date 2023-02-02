# Amazon EventBridge Pipes error handling and troubleshooting<a name="eb-pipes-error-troubleshooting"></a>

## Retry behavior and error handling<a name="pipes-error-handling"></a>

EventBridge Pipes automatically retries enrichment and target invocation on any retryable AWS failures with the source service, the enrichment or target services, or EventBridge\. However, if there are failures returned by enrichment or target customer implementations, the pipe polling throughput will gradually back off\. For nearly continuous 4xx errors \(such as authorization problems with IAM or missing resources\), the pipe can be automatically disabled with an explanatory message in the `StateReason`\.

A pipe inherits dead\-letter queue \(DLQ\) behavior from the source\. If the source Amazon SQS queue has a configured DLQ, messages are automatically delivered there after the configured number of attempts\. For stream sources, such as DynamoDB and Kinesis streams, you can configure a DLQ for the pipe and route events\.

## Troubleshooting<a name="pipes-troubleshooting"></a>

Creating, deleting, and updating pipes are asynchronous operations that might result in a failure state\. Likewise, a pipe might be automatically disabled due to failures\. In all cases, the pipe `StateReason` provides information to help troubleshoot the failure\.

The following is a sample of the possible `StateReason` values:
+ Stream not found\. To resume processing please delete the pipe and create a new one\.
+ Pipes does not have required permissions to perform Queue operations \(sqs:ReceiveMessage, sqs:DeleteMessage and sqs:GetQueueAttributes\)
+ Connection error\. Your VPC must be able to connect to pipes\. You can provide access by configuring a NAT Gateway\. For how to setup NAT gateway, please check AWS documentation\.
+ MSK cluster does not have security groups associated with it

A pipe may be automatically stopped with an updated `StateReason`\. Possible reasons include:
+ A Step Functions standard workflow configured as an [enrichment](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-pipes.html#pipes-enrichment)\.
+ A Step Functions standard workflow configured as as a target to be [invoked synchronously](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-pipes.html#pipes-invocation)\.

If you specify a `DeadLetterConfig` for a pipe with a Kinesis or DynamoDB source, make sure that the `MaximumRecordAgeInSeconds` property on the pipe is less than the `MaximumRecordAge` of the source event\. `MaximumRecordAgeInSeconds` controls when the pipe poller will give up on the event and deliver it to the DLQ and the `MaximumRecordAge` controls how long the message will be visible in the source stream before it gets deleted\. Therefore, set `MaximumRecordAgeInSeconds` to a value that is less than the source `MaximumRecordAge` so that there's adequate time between when the event gets sent to the DLQ, and when it gets automatically deleted by the source for you to determine why the event went to the DLQ\.

### Custom encryption failures<a name="pipes-error-handling-cms"></a>

If you configure a source to use an AWS KMS custom encryption key \(CMK\), rather than an AWS\-managed AWS KMS key, you must explicitly give your pipe's Execution Role decryption permission\. To do so, include the following additional permission in the custom CMK policy:

```
  {
      "Sid": "Allow Pipes access",
      "Effect": "Allow",
      "Principal": {
          "AWS": "arn:aws:iam::01234567890:role/service-role/Amazon_EventBridge_Pipe_DDBStreamSourcePipe_12345678"
      },
      "Action": "kms:Decrypt",
      "Resource": "*"
  }
```

Replace the above role with your pipe's Execution Role\.

This is true for all pipe sources with AWS KMS CMK, including Amazon DynamoDB Streams, Amazon Kinesis Data Streams, Amazon MSK, Amazon SQS, and Amazon MQ\.