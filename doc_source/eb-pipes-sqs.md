# Amazon Simple Queue Service as a source<a name="eb-pipes-sqs"></a>

You can use EventBridge Pipes to receive records from an Amazon SQS queue\. You can then optionally filter or enhance these records before sending them to an available destination for processing\. There are source\-specific settings that you can choose when setting up the pipe\. EventBridge Pipes maintains the order of the records received from the cluster when it sends that data to the destination\.

You can use a pipe to process messages in an Amazon Simple Queue Service \(Amazon SQS\) queue\. EventBridge Pipes support [standard queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/standard-queues.html) and [first\-in, first\-out \(FIFO\) queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html)\. With Amazon SQS, you can offload tasks from one component of your application by sending them to a queue and processing them asynchronously\.

EventBridge polls the queue and invokes your pipe synchronously with an event that contains queue messages\. EventBridge reads messages in batches and invokes your pipe once for each batch\. When your pipe successfully processes a batch, EventBridge deletes its messages from the queue\.

By default, EventBridge polls up to 10 messages in your queue simultaneously and sends that batch to your pipe\. To avoid invoking the pipe with a small number of records, you can tell the event source to buffer records for up to five minutes by configuring a batch window\. Before invoking the pipe, EventBridge continues to poll messages from the Amazon SQS standard queue until one of these things occurs:
+ The batch window expires\.
+ The invocation payload size quota is reached\.
+ The configured maximum batch size is reached\.

**Note**  
If you're using a batch window and your Amazon SQS queue contains low traffic, EventBridge might wait for up to 20 seconds before invoking your pipe\. This is true even if you set a batch window for fewer than 20 seconds\. For FIFO queues, records contain additional attributes that are related to deduplication and sequencing\.

When EventBridge reads a batch, the messages stay in the queue but are hidden for the length of the queue's [visibility timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)\. If your pipe successfully processes the batch, EventBridge deletes the messages from the queue\. By default, if your pipe encounters an error while processing a batch, all messages in that batch become visible in the queue again\. For this reason, your pipe code must be able to process the same message multiple times without unintended side effects\. You can modify this reprocessing behavior by including batch item failures in your pipe response\. The following example shows an event for a batch of two messages\.

**Example events**

The following sample event shows the information that is received by the pipe\. You can use this event to create and filter your event patterns, or to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

**Standard queue**

```
[
  {
    "messageId": "059f36b4-87a3-44ab-83d2-661975830a7d",
    "receiptHandle": "AQEBwJnKyrHigUMZj6rYigCgxlaS3SLy0a...",
    "body": "Test message.",
    "attributes": {
      "ApproximateReceiveCount": "1",
      "SentTimestamp": "1545082649183",
      "SenderId": "AIDAIENQZJOLO23YVJ4VO",
      "ApproximateFirstReceiveTimestamp": "1545082649185"
    },
    "messageAttributes": {},
    "md5OfBody": "e4e68fb7bd0e697a0ae8f1bb342846b3",
    "eventSource": "aws:sqs",
    "eventSourceARN": "arn:aws:sqs:us-east-2:123456789012:my-queue",
    "awsRegion": "us-east-2"
  },
  {
    "messageId": "2e1424d4-f796-459a-8184-9c92662be6da",
    "receiptHandle": "AQEBzWwaftRI0KuVm4tP+/7q1rGgNqicHq...",
    "body": "Test message.",
    "attributes": {
      "ApproximateReceiveCount": "1",
      "SentTimestamp": "1545082650636",
      "SenderId": "AIDAIENQZJOLO23YVJ4VO",
      "ApproximateFirstReceiveTimestamp": "1545082650649"
    },
    "messageAttributes": {},
    "md5OfBody": "e4e68fb7bd0e697a0ae8f1bb342846b3",
    "eventSource": "aws:sqs",
    "eventSourceARN": "arn:aws:sqs:us-east-2:123456789012:my-queue",
    "awsRegion": "us-east-2"
  }
]
```

**FIFO queue**

```
[
  {
    "messageId": "11d6ee51-4cc7-4302-9e22-7cd8afdaadf5",
    "receiptHandle": "AQEBBX8nesZEXmkhsmZeyIE8iQAMig7qw...",
    "body": "Test message.",
    "attributes": {
      "ApproximateReceiveCount": "1",
      "SentTimestamp": "1573251510774",
      "SequenceNumber": "18849496460467696128",
      "MessageGroupId": "1",
      "SenderId": "AIDAIO23YVJENQZJOL4VO",
      "MessageDeduplicationId": "1",
      "ApproximateFirstReceiveTimestamp": "1573251510774"
    },
    "messageAttributes": {},
    "md5OfBody": "e4e68fb7bd0e697a0ae8f1bb342846b3",
    "eventSource": "aws:sqs",
    "eventSourceARN": "arn:aws:sqs:us-east-2:123456789012:fifo.fifo",
    "awsRegion": "us-east-2"
  }
]
```

## Scaling and processing<a name="pipes-sqs-scaling"></a>

For standard queues, EventBridge uses [long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html) to poll a queue until it becomes active\. When messages are available, EventBridge reads up to five batches and sends them to your pipe\. If messages are still available, EventBridge increases the number of processes that are reading batches by up to 60 more instances per minute\. The maximum number of batches that a pipe can process simultaneously is 1,000\.

For FIFO queues, EventBridge sends messages to your pipe in the order that it receives them\. When you send a message to a FIFO queue, you specify a [message group ID](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/using-messagegroupid-property.html)\. Amazon SQS facilitates delivering messages in the same group to EventBridge, in order\. EventBridge sorts the received messages into groups and sends only one batch at a time for a group\. If your pipe returns an error, the pipe attempts all retries on the affected messages before EventBridge receives additional messages from the same group\.

## Configuring a queue to use with EventBridge Pipes<a name="pipes-sqs-configure-queue"></a>

[Create an Amazon SQS queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html) to serve as an source for your pipe\. Then configure the queue to allow time for your pipe to process each batch of events—and for EventBridge to retry in response to throttling errors as it scales up\.

To allow your pipe time to process each batch of records, set the source queue's visibility timeout to at least six times the timeout that you configure on your pipe\. The extra time allows for EventBridge to retry if your pipe is throttled while processing a previous batch\.

If your pipe fails to process a message multiple times, Amazon SQS can send it to a [dead\-letter queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)\. When your pipe returns an error, EventBridge keeps it in the queue\. After the visibility timeout occurs, EventBridge receives the message again\. To send messages to a second queue after a number of receives, configure a dead\-letter queue on your source queue\.

**Note**  
Make sure that you configure the dead\-letter queue on the source queue, not on the pipe\. The dead\-letter queue that you configure on a pipe is used for the pipe's asynchronous invocation queue, not for source queues\.

If your pipe returns an error, or can't be invoked because it's at maximum concurrency, processing might succeed with additional attempts\. To give messages more chances to be processed before sending them to the dead\-letter queue, set the `maxReceiveCount` on the source queue's redrive policy to at least **5**\.

## Execution role permissions<a name="pipes-sqs-perms"></a>

When setting up a pipe, you can use an existing execution role, or have EventBridge create one for you with the needed permissions\. For Amazon SQS, EventBridge requires the following permissions to manage resources that are related to your Amazon SQS queue\. If you’re setting up your own execution role, you must add these permissions yourself\.
+ [sqs:ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html)
+ [sqs:DeleteMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessage.html)
+ [sqs:GetQueueAttributes](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_GetQueueAttributes.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

## Configuring a pipe with Amazon SQS as the source<a name="pipes-sqs-configure"></a>

### Adding the Amazon SQS queue source<a name="pipes-sqs-configure-source"></a>

**To add an Amazon SQS source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **SQS**\.

1. For **SQS queue**, choose the queue you want to use\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Batch size \- optional**, enter a maximum number of records for each batch\. The default value is 100\.

   1. For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-sqs-configure-filtering"></a>

You can add filtering to your pipe so that you’re only sending a subset of records from your Amazon MQ queue to the target\.

**To configure filtering by using the console**

1. Choose **Filtering**\.

1. Under **Sample event \- optional**, you’ll see a sample Amazon MQ event you can use to build your event pattern, or you can enter your own event by choosing **Enter your own**\.

1. Under **Event pattern**, enter the event pattern that you want to filter the records\. For more information about building event patterns, see [Amazon EventBridge event patterns](eb-event-patterns.md)\.

   The following is an example event pattern that only sends events with the value **Seattle** in the **City** field\.

   ```
   {
     "data": {
       "City": ["Seattle"]
     }
   }
   ```

Now that events are being filtered, you can add optional enrichment and a target for the pipe\.

### \(Optional\) Defining enrichment<a name="pipes-sqs-define-enrichment"></a>

You can send the event data to a Lambda function, AWS Step Functions state machine, Amazon API Gateway, or API destination for enrichment\.

**To select enrichment**

1. Choose **Enrichment**\.

1. Under **Details**, for **Service**, select the service and related settings you want to use for enrichment\.

You can also transform the data before sending it to be enhanced\.

**\(Optional\) To define the input transformer**

1. Choose **Enrichment Input Transformer \- optional**\.

1. For **Sample events/Event Payload**, choose the sample event type\.

1. For **Transformer**, enter the transformer syntax, such as `"Event happened at <$.detail.field>."` where `<$.detail.field>` is a reference to a field from the sample event\. You can also double\-click a field from the sample event to add it to the transformer\.

1. For **Output**, verify that the output looks like you want it to\.

Now that the data has been filtered and enhanced, you must define a target to send the event data to\.

### Configuring a target<a name="pipes-sqs-configure-target"></a>

**To configure a target**

1. Choose **Target**\.

1. Under **Details**, for **Target service**, choose the a target\. The fields that display vary depending on the target that you choose\. Enter information specific to this target type, as needed\.

You can also transform the data before sending it to the target\.

**\(Optional\) To define the input transformer**

1. Choose **Target Input Transformer \- optional**\.

1. For **Sample events/Event Payload**, choose the sample event type\.

1. For **Transformer**, enter the transformer syntax, such as `"Event happened at <$.detail.field>."` where `<$.detail.field>` is a reference to a field from the sample event\. You can also double\-click a field from the sample event to add it to the transformer\.

1. For **Output**, verify that the output looks like you want it to\.

Now that the pipe is configured, make sure that its settings are configured correctly\.

### Configure the pipe settings<a name="pipes-sqs-configure-pipe-settings"></a>

A pipe is active by default, but you can deactivate it\. You can also specify the permissions of the pipe and add tags\.

**To configure the pipe settings**

1. Choose the **Pipe settings** tab\.

1. By default, newly created pipes are active as soon as they're created\. If you want to create an inactive pipe, under **Activation**, for **Activate pipe**, turn off **Active**\.

1. Under **Permissions**, for **Execution role**, do one of the following:

   1. To have EventBridge create a new execution role for this pipe, choose **Create a new role for this specific resource\.** Under **Role name**, you can optionally edit the role name\.

   1. To use an existing execution role, choose **Use existing role**\. Under **Role name**, choose the role\.

1. \(Optional\) Under **Tags \- optional**, choose **Add new tag** and enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create pipe**\.

## Reporting batch item failures<a name="pipes-sqs-batch-failures"></a>

When your pipe encounters an error while processing a batch, all messages in that batch become visible in the queue again, including messages that EventBridge processed successfully\. As a result, your pipe can end up processing the same message multiple times\. EventBridge allows partial successes and by default, retries only specific failed records\. This helps to reduce the number of retries for a record\. However, it doesn’t prevent the possibility of retries for a successful record\.

### Success and failure conditions<a name="pipes-sqs-batch-failures-conditions"></a>

If you return any of the following, EventBridge treats a batch as a complete success:
+ An empty `batchItemFailure` list
+ A null `batchItemFailure` list
+ An empty `EventResponse`
+ A null `EventResponse`

If you return any of the following, EventBridge treats a batch as a complete failure:
+ An invalid JSON response
+ An empty string `itemIdentifier`
+ A null `itemIdentifier`
+ An `itemIdentifier` with a bad key name
+ An `itemIdentifier` value with a message ID that doesn't exist

EventBridge retries failures based on your retry strategy\.