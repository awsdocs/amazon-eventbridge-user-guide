# Event retry policy and using dead\-letter queues<a name="rule-dlq"></a>

Sometimes an event is not successfully delivered to the target specified in a rule\. This can happen when, for example, the target resource is unavailable, when EventBridge lacks permissions to access the target resource, or due to network conditions\. When an event is not successfully delivered to a target, EventBridge retries sending the event\. The length of time and number of retry attempts is determined by the **Retry policy** settings for the target\. By default, EventBridge retries sending the event for 24 hours and up to 185 times with an [exponential back off and jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) \(randomized delay\)\. If an event is not delivered before all retry attempts are exhausted, the event is dropped and EventBridge does not continue to process it\. To avoid losing events after they fail to be delivered to a target, you can configure a dead\-letter queue \(DLQ\) and send all failed events to it for processing later\.

EventBridge DLQs are standard Amazon SQS queues that EventBridge uses to store events that could not successfully be delivered to a target\. When you create a rule and add a target, you can choose whether or not to use a DLQ\. When you configure a DLQ, you can retain any events that were not successfully delivered\. This lets you resolve the issue that resulted in the failed invocation and then process the events at a later time\.

Not all event errors are handled in the same way\. Some events are dropped or sent to a DLQ without any retry attempts\. For example, for errors that result from missing permissions to a target, or a target resource that no longer exists, all retry attempts will fail until an action is taken to resolve the underlying issue\. EventBridge sends these events directly to the DLQ, if configured, upon delivery failure\.

When an event fails to successfully be delivered to a target, EventBridge publishes an event to Amazon CloudWatch metrics, including indicating that a target invocation failed\. If you use a DLQ, additional metrics are sent to CloudWatch including `InvocationsSentToDLQ` and `InvocationsFailedToBeSentToDLQ`\. To learn more about EventBridge metrics, see [Monitoring Usage with CloudWatch Metrics](eventbridge-monitoring-cloudwatch-metrics.md)\.

## Considerations for using a dead\-letter queue<a name="dlq-considerations"></a>

Consider the following when configuring a DLQ for EventBridge\.
+ Only [standard queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/standard-queues.html) are supported\. You cannot use a FIFO queue for a DLQ in EventBridge\.
+ EventBridge includes event metadata and message attributes in the message, including: the Error Code, Error Message, the Exhausted Retry Condition, Rule ARN, Retry Attempts, and the Target ARN\. You can use these values to identify an event and the cause of the failure\.
+ Permissions for DLQs in the same account:
  + If you add a target to a rule using the console, and you choose an Amazon SQS queue in the same account, a resource\-based policy that grants EventBridge access to the queue is attached to the queue for you\.
  + If you use the `PutTargets` operation of the EventBridge API to add or update a target for a rule, and you choose an Amazon SQS queue in the same account, you must manually grant permissions to the queue selected\. To learn more, see [Granting permissions to the DLQ](#dlq-perms)\.
+ Permissions for using Amazon SQS queues from a different AWS account\.
  + If you create a rule from the console, queues from other accounts are not displayed for you to select\. You must provide the ARN for the queue in the other account, and then manually attach a resource\-based policy to grant permission to the queue\. To learn more, see [Granting permissions to the DLQ](#dlq-perms)\.
  + If you create a rule using the API, you must manually attach a resource\-based policy to the SQS queues in another account that is used as the DLQ\. To learn more, see [Granting permissions to the DLQ](#dlq-perms)\.
+ The Amazon SQS queue you use must be in the same Region in which you create the rule\.

## Granting permissions to the DLQ<a name="dlq-perms"></a>

When you configure a DLQ for a target of a rule, EventBridge sends the events with failed invocations to the Amazon SQS queue selected\. To successfully deliver events to the queue, EventBridge must have permission to do so\. When you configure a target for a rule and select a DLQ using the EventBridge console, the permissions are automatically added\. If you create a rule using the API, or use a queue that is in a different AWS account, you must manually create a resource\-based policy that grants the required permissions and then attach it to the queue\.

The following resource\-based policy demonstrates how to grant the required permissions for EventBridge to send event messages to an Amazon SQS queue\. The policy example grants the EventBridge service permissions to use the `SendMessage` operation to send messages to a queue named "MyEventDLQ"\. The queue must be in the us\-west\-2 Region in AWS account 123456789012\. The `Condition` statement allows only requests that come from a rule named "MyTestRule" that is created in the us\-west\-2 Region in the AWS account 123456789012\.

```
{
  "Sid": "Dead-letter queue permissions",
  "Effect": "Allow",
  "Principal": {
     "Service": "events.amazonaws.com"
  },
  "Action": "sqs:SendMessage",
  "Resource": "arn:aws:sqs:us-west-2:123456789012:MyEventDLQ",
  "Condition": {
    "ArnEquals": {
      "aws:SourceArn": "arn:aws:events:us-west-2:123456789012:rule/MyTestRule"
    }
  }
}
```

To attach the policy to the queue, use the Amazon SQS console, open the queue, then choose the **Access policy** and edit the policy\. You can also use the AWS CLI, to learn more see [Amazon SQS Permissions](resource-based-policies-eventbridge.md#sqs-permissions)\.

## How to resend events from a DLQ<a name="dlq-resend"></a>

You can move messages out of a DLQ in two ways:
+ Avoid writing Amazon SQS consumer logic – Set your DLQ as an event source to the Lambda function to drain your DLQ\.
+ Write Amazon SQS consumer logic – Use the Amazon SQS API, AWS SDK, or AWS CLI to write custom consumer logic for polling, processing, and deleting the messages in the DLQ\.