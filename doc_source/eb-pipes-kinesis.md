# Amazon Kinesis stream as a source<a name="eb-pipes-kinesis"></a>

You can use EventBridge Pipes to receive records in a Kinesis data stream\. You can then optionally filter or enhance these records before sending them to one of the available destinations for processing\. There are settings specific to Kinesis that you can choose when setting up the pipe\. EventBridge Pipes maintains the order of records from the data stream when sending that data to the destination\.

A Kinesis data stream is a set of [shards](https://docs.aws.amazon.com/kinesis/latest/dev/key-concepts.html#shard)\. Each shard contains a sequence of data records\. A **consumer** is an application that processes the data from a Kinesis data stream\. You can map an EventBridge Pipe to a shared\-throughput consumer \(standard iterator\), or to a dedicated\-throughput consumer with [enhanced fan\-out](https://docs.aws.amazon.com/kinesis/latest/dev/enhanced-consumers.html)\.

For standard iterators, EventBridge uses the HTTP protocol to poll each shard in your Kinesis stream for records\. The pipe shares the read throughput with other consumers of the shard\.

To minimize latency and maximize read throughput, you can create a data stream consumer with enhanced fan\-out\. Stream consumers get a dedicated connection to each shard that doesn't impact other applications reading from the stream\. The dedicated throughput can help if you have many applications reading the same data, or if you're reprocessing a stream with large records\. Kinesis pushes records to EventBridge over HTTP/2\. For information about Kinesis data streams, see [ Reading Data from Amazon Kinesis Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/building-consumers.html)\.

By default, EventBridge invokes your pipe as soon as records are available\. If the batch that EventBridge reads from the source has only one record in it, only one event is processed\. To avoid processing a small number of records, you can tell the pipe to buffer records for up to five minutes by configuring a batching window\. Before processing the events, EventBridge continues to read records from the source until it has gathered a full batch, the batching window expires, or the batch reaches the payload limit of 6 MB\. 

**Example event**

The following sample event shows the information that is received by the pipe\. You can use this event to create and filter your event patterns, or to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

```
[
  {
    "kinesisSchemaVersion": "1.0",
    "partitionKey": "1",
    "sequenceNumber": "49590338271490256608559692538361571095921575989136588898",
    "data": "SGVsbG8sIHRoaXMgaXMgYSB0ZXN0Lg==",
    "approximateArrivalTimestamp": 1545084650.987
    "eventSource": "aws:kinesis",
    "eventVersion": "1.0",
    "eventID": "shardId-000000000006:49590338271490256608559692538361571095921575989136588898",
    "eventName": "aws:kinesis:record",
    "invokeIdentityArn": "arn:aws:iam::123456789012:role/lambda-role",
    "awsRegion": "us-east-2",
    "eventSourceARN": "arn:aws:kinesis:us-east-2:123456789012:stream/lambda-stream"
  },
  {
    "kinesisSchemaVersion": "1.0",
    "partitionKey": "1",
    "sequenceNumber": "49590338271490256608559692540925702759324208523137515618",
    "data": "VGhpcyBpcyBvbmx5IGEgdGVzdC4=",
    "approximateArrivalTimestamp": 1545084711.166
    "eventSource": "aws:kinesis",
    "eventVersion": "1.0",
    "eventID": "shardId-000000000006:49590338271490256608559692540925702759324208523137515618",
    "eventName": "aws:kinesis:record",
    "invokeIdentityArn": "arn:aws:iam::123456789012:role/lambda-role",
    "awsRegion": "us-east-2",
    "eventSourceARN": "arn:aws:kinesis:us-east-2:123456789012:stream/lambda-stream"
  }
]
```

## Execution role permissions<a name="pipes-ak-perms"></a>

When setting up a pipe, you can use an existing execution role, or have EventBridge create one for you with the needed permissions\. For Kinesis, EventBridge Pipes requires the following permissions to manage resources that are related to your Kinesis data stream\. If you’re setting up your own execution role, you must add these permissions yourself\.
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DescribeStream.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DescribeStream.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DescribeStreamSummary.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DescribeStreamSummary.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetRecords.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetRecords.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetShardIterator.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetShardIterator.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListShards.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListShards.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListStreams.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListStreams.html)
+ [https://docs.aws.amazon.com/kinesis/latest/APIReference/API_SubscribeToShard.html](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_SubscribeToShard.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

## Configuring a pipe with Kinesis as the source<a name="pipes-ak-configure"></a>

### Adding the Kinesis source<a name="pipes-ak-configure-source"></a>

**To add a Kinesis source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **Kinesis**\.

1. For **Kinesis stream**, choose the stream that you want to use as a source\.

1. For **Starting position**, choose one of the following:
   + **Latest** – Start reading the stream with the most recent record in the shard\.
   + **Trim horizon** – Start reading the stream with the last untrimmed record in the shard\. This is the oldest record in the shard\.
   + **At timestamp** – Start reading the stream from a specified time\. Under **Timestamp**, enter a data and time using YYYY/MM/DD and hh:mm:ss format\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Batch size \- optional**, enter a maximum number of records for each batch\. The default value is 100\.

   1. \(Optional\) For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

   1. For **Concurrent batches per shard \- optional**, enter the number of batches from the same shard that can be read at the same time\.

   1. For **On partial batch item failure**, choose the following:
      + **AUTOMATIC\_BISECT** – Halve each batch and retry each half until all the records are processed or there is one failed message remaining in the batch\.
**Note**  
If you don't choose **AUTOMATIC\_BISECT**, you can return specific failed records and only those get retried\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-ak-configure-filtering"></a>

You can add filtering to your pipe so you’re sending only a subset of records from your DynamoDB stream to the target\.

**To configure filtering by using the console**

1. Choose **Filtering**\.

1. Under **Sample event \- optional**, you’ll see a sample Kinesis stream event that you can use to build your event pattern, or you can enter your own event by choosing **Enter your own**\.

1. Under **Event pattern**, enter the event pattern that that you want to filter the records\. For more information about building event patterns, see [Amazon EventBridge event patterns](eb-event-patterns.md)\.

   The following is an example event pattern that only sends events with the value **Seattle** in the **City** field\.

   ```
   {
     "data": {
       "City": ["Seattle"]
     }
   }
   ```

Now that events are being filtered, you can add optional enrichment and a target for the pipe\.

### \(Optional\) Defining enrichment<a name="pipes-ak-define-enrichment"></a>

You can send the event data for enrichment to a Lambda function, AWS Step Functions state machine, Amazon API Gateway, or API destination\.

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

### Configuring a target<a name="pipes-ak-configure-target"></a>

**To configure a target**

1. Choose **Target**\.

1. Under **Details**, for **Target service**, choose the target\. The fields that display vary depending on the target that you choose\. Enter information specific to this target type, as needed\.

You can also transform the data before sending it to the target\.

**\(Optional\) To define the input transformer**

1. Choose **Target Input Transformer \- optional**\.

1. For **Sample events/Event Payload**, choose the sample event type\.

1. For **Transformer**, enter the transformer syntax, such as `"Event happened at <$.detail.field>."` where `<$.detail.field>` is a reference to a field from the sample event\. You can also double\-click a field from the sample event to add it to the transformer\.

1. For **Output**, verify that the output looks like you want it to\.

Now that the pipe is configured, make sure that its settings are configured correctly\.

### Configuring the pipe settings<a name="pipes-ak-configure-pipe-settings"></a>

A pipe is active by default, but you can deactivate it\. You can also specify the permissions of the pipe and add tags\.

**To configure the pipe settings**

1. Choose the **Pipe settings** tab\.

1. By default, newly created pipes are active as soon as they're created\. If you want to create an inactive pipe, under **Activation**, for **Activate pipe**, turn off **Active**\.

1. Under **Permissions**, for **Execution role**, do one of the following:

   1. To have EventBridge create a new execution role for this pipe, choose **Create a new role for this specific resource\.** Under **Role name**, you can optionally edit the role name\.

   1. To use an existing execution role, choose **Use existing role**\. Under **Role name**, choose the role\.

1. \(Optional\) For **Retry policy and Dead\-letter queue \- optional**, do the following:

   Under **Retry policy**, do the following:

   1. By default, newly created pipes don't have a retry policy turned on\. If you want to turn on retry policies, turn on **Retry**\.

   1. For **Maximum age of event**, enter a value between one minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. By default, newly created pipes don't use a dead\-letter queue \(DLQ\)\. If you want to use a DLQ, turn on **Dead\-letter queue**, choose the method of your choice, and choose the queue or topic you'd like to use\.

1. \(Optional\) Under **Tags \- optional**, choose **Add new tag** and enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create pipe**\.

## Reporting batch item failures<a name="pipes-ak-batch-failures"></a>

When EventBridge consumes and processes streaming data from an source, by default it checkpoints to the highest sequence number of a batch, but only when the batch is a complete success\. EventBridge also allows partial successes and, by default, retries only specific failed records\. This helps to reduce the number of retries for a record\. However, it doesn’t prevent the possibility of retries for a successful record\.

### Success and failure conditions<a name="pipes-ak-batch-failures-conditions"></a>

If you return any of the following, EventBridge treats a batch as a complete success:
+ An empty `batchItemFailure` list
+ A null `batchItemFailure` list
+ An empty `EventResponse`
+ A null `EventResponse`

If you return any of the following, EventBridge treats a batch as a complete failure:
+ An empty string `itemIdentifier`
+ A null `itemIdentifier`
+ An `itemIdentifier` with a bad key name

EventBridge retries failures based on your retry strategy\.