# Amazon DynamoDB stream as a source<a name="eb-pipes-dynamodb"></a>

You can use EventBridge Pipes to receive records in a DynamoDB stream\. You can then optionally filter or enhance these records before sending them to a target for processing\. There are settings specific to Amazon DynamoDB Streams that you can choose when setting up the pipe\. EventBridge Pipes maintains the order of records from the data stream when sending that data to the destination\.

EventBridge polls shards in your DynamoDB stream for records at a base rate of four times per second\. When records are available, EventBridge processes the event and waits for the result\. If processing succeeds, EventBridge resumes polling until it receives more records\.

By default, EventBridge invokes your pipe as soon as records are available\. If the batch that EventBridge reads from the source has only one record in it, only one event is processed\. To avoid processing a small number of records, you can tell the pipe to buffer records for up to five minutes by configuring a batching window\. Before processing the events, EventBridge continues to read records from the source until it has gathered a full batch, the batching window expires, or the batch reaches the payload limit of 6 MB\.

**Important**  
Disabling a DynamoDB stream that is the source of a pipe results in that pipe becoming unusable, even if you then re\-enable the stream\. This happens because:  
You cannot stop, start, or update a pipe whose source is disabled\.
You cannot update a pipe with a new source after creation\. When you re\-enable a DynamoDB stream, that stream is assigned a new Amazon Resource Name \(ARN\), and is no longer associated with your pipe\.
If you do re\-enable the DynamoDB stream, you will then need to create a new pipe using the stream's new ARN\.

**Example event**

The following sample event shows the information that's received by the pipe\. You can use this event to create and filter your event patterns , or to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

```
[
  {
    "eventID": "1",
    "eventVersion": "1.0",
    "dynamodb": {
      "Keys": {
        "Id": {
          "N": "101"
        }
      },
      "NewImage": {
        "Message": {
          "S": "New item!"
        },
        "Id": {
          "N": "101"
        }
      },
      "StreamViewType": "NEW_AND_OLD_IMAGES",
      "SequenceNumber": "111",
      "SizeBytes": 26
    },
    "awsRegion": "us-west-2",
    "eventName": "INSERT",
    "eventSourceARN": "arn:aws:dynamodb:us-east-1:111122223333:table/EventSourceTable",
    "eventSource": "aws:dynamodb"
  },
  {
    "eventID": "2",
    "eventVersion": "1.0",
    "dynamodb": {
      "OldImage": {
        "Message": {
          "S": "New item!"
        },
        "Id": {
          "N": "101"
        }
      },
      "SequenceNumber": "222",
      "Keys": {
        "Id": {
          "N": "101"
        }
      },
      "SizeBytes": 59,
      "NewImage": {
        "Message": {
          "S": "This item has changed"
        },
        "Id": {
          "N": "101"
        }
      },
      "StreamViewType": "NEW_AND_OLD_IMAGES"
    },
    "awsRegion": "us-west-2",
    "eventName": "MODIFY",
    "eventSourceARN": "arn:aws:dynamodb:us-east-1:111122223333:table/EventSourceTable",
    "eventSource": "aws:dynamodb"
  }
]
```

## Execution role permissions<a name="pipes-ddb-perms"></a>

When settings up a pipe, you can use an existing execution role, or have EventBridge create one for you with the needed permissions\. For DynamoDB Streams, EventBridge Pipes requires the following permissions to manage resources that are related to your DynamoDB data stream\. If you’re setting up your own execution role, you must add these permissions yourself\.
+ [https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_DescribeStream.html)
+ [https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetRecords.html](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetRecords.html)
+ [https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetShardIterator.html](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_GetShardIterator.html)
+ [https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_streams_ListStreams.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

## Configuring a pipe with DynamoDB as the source<a name="pipes-ddb-configure"></a>

### Adding the DynamoDB source<a name="pipes-ddb-configure-source"></a>

**To add a DynamoDB source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **DynamoDB**\.

1. For **DynamoDB stream**, choose the stream you want to use as a source\.

1. For **Starting position**, choose one of the following:
   + **Latest** – Start reading the stream with the most recent record in the shard\.
   + **Trim horizon** – Start reading the stream with the last untrimmed record in the shard\. This is the oldest record in the shard\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Batch size \- optional**, enter a maximum number of records for each batch\. The default value is 100\.

   1. For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

   1. For **Concurrent batches per shard \- optional**, enter the number of batches from the same shard that can be read at the same time\.

   1. For **On partial batch item failure**, choose the following:
      + **AUTOMATIC\_BISECT** – Halve each batch and retry each half until all the records are processed or there is one failed message remaining in the batch\.
**Note**  
If you don't choose **AUTOMATIC\_BISECT**, you can return specific failed records and only those get retried\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-ddb-configure-filtering"></a>

You can add filtering to your pipe so that you’re sending only a subset of records from your DynamoDB stream to the target\.

**To configure filtering by using the console**

1. Choose **Filtering**\.

1. Under **Sample event \- optional**, you’ll see a sample DynamoDB stream event that you can use to build your event pattern, or you can enter your own by choosing **Enter your own**\.

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

### \(Optional\) Defining enrichment<a name="pipes-ddb-define-enrichment"></a>

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

### Configuring a target<a name="pipes-ddb-configure-target"></a>

**To configure a target**

1. Choose **Target**\.

1. Under **Details**, for **Target service**, choose a target\. The fields that display vary depending on the target that you choose\. Enter information specific to this target type, as needed\.

You can also transform the data before sending it to the target\.

**\(Optional\) To define the input transformer**

1. Choose **Target Input Transformer \- optional**\.

1. For **Sample events/Event Payload**, choose the sample event type\.

1. For **Transformer**, enter the transformer syntax, such as `"Event happened at <$.detail.field>."` where `<$.detail.field>` is a reference to a field from the sample event\. You can also double\-click a field from the sample event to add it to the transformer\.

1. For **Output**, verify that the output looks like you want it to\.

Now that the pipe is configured, make sure that its settings are configured correctly\.

### Configuring the pipe settings<a name="pipes-ddb-configure-pipe-settings"></a>

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

## Reporting batch item failures<a name="pipes-ddb-batch-failures"></a>

When EventBridge consumes and processes streaming data from an source, by default it checkpoints to the highest sequence number of a batch, but only when the batch is a complete success\. EventBridge also allows partial successes and, by default, retries only specific failed records\. This helps to reduce the number of retries for a record\. However, it doesn’t prevent the possibility of retries for a successful record\.

### Success and failure conditions<a name="pipes-ddb-batch-failures-conditions"></a>

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