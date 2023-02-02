# Amazon EventBridge Pipes filtering<a name="eb-pipes-event-filtering"></a>

With EventBridge Pipes, you can filter a given source’s events and process only a subset of them\. This filtering works in the same way as filtering on an EventBridge event bus or Lambda event source mapping, by using event patterns\. For more information about event patterns, see [Amazon EventBridge event patterns](eb-event-patterns.md)\.

A filter criteria `FilterCriteria` object is a structure that consists of a list of filters \(`Filters`\)\. Each filter is a structure that defines an filtering pattern \(`Pattern`\)\. A `Pattern` is a string representation of a JSON filter rule\. A `FilterCriteria` object looks like the following example:

```
{
  "Filters": [
    {"Pattern": "{ \"Metadata1\": [ rule1 ], \"data\": { \"Data1\": [ rule2 ] }}"
    }
  ]
}
```

For added clarity, here is the value of the filter's `Pattern` expanded in plain JSON:

```
{
  "Metadata1": [ pattern1 ],
  "data": {"Data1": [ pattern2 ]}
}
```

The main parts to a `FilterCriteria` object are metadata properties and data properties\.
+ **Metadata properties** are the fields of the event object\. In the example, `FilterCriteria.Metadata1` refers to a metadata property\.
+ **Data properties** are the fields of the event body\. In the example, `FilterCriteria.Data1` refers to a data property\.

For example, suppose your Kinesis stream contains an event like this::

```
{
  "kinesisSchemaVersion": "1.0",
  "partitionKey": "1",
  "sequenceNumber": "49590338271490256608559692538361571095921575989136588898",
  "data": {"City": "Seattle",
    "State": "WA",
    "Temperature": "46",
    "Month": "December"
  },
  "approximateArrivalTimestamp": 1545084650.987
}
```

When the event flows through your pipe, it'll look like the following with the `data` field base64\-encoded:

```
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
```

The *metadata properties* on the Kinesis event are any field outside of the `data` object, such as `partitionKey` or `sequenceNumber`\.

The *data properties* of the Kinesis event are the fields inside the `data` object, such as `City` or `Temperature`\.

When you filter to match this event, you can use filters on the decoded fields\. For example, to filter on `partitionKey` and `City` you'd use the following filter:

```
{ "partitionKey": [ "1" ], "data": { "City": [ "Seattle" ] }
```

When you’re creating event filters, EventBridge Pipes can access event content\. This content is either JSON\-escaped, such as the Amazon SQS `body` field, or base64\-encoded, such as the Kinesis `data` field\. If your data is valid JSON, your input templates or JSON paths for target parameters can reference the content directly\. For example, if a Kinesis event source is valid JSON, you can reference a variable using `<$.data.someKey>`\.

When creating event patterns, you can filter based on the fields sent by the source API, and not on fields added by the polling operation\. The following fields can't be used in event patterns:
+ `awsRegion`
+ `eventSource`
+ `eventSourceARN`
+ `eventVersion`
+ `eventID`
+ `eventName`
+ `invokeIdentityArn`
+ `eventSourceKey`

## Message and data fields<a name="pipes-filter-fields"></a>

Every EventBridge Pipe source contains a field which contains the core message or data\. We refer to these as *message* fields or *data* fields\. These fields are special because they may be JSON\-escaped or base64\-encoded, but when they are valid JSON they can be filtered with JSON patterns as if the body was not escaped\. The contents of these fields can also be used in [input transformers](eb-pipes-input-transformation.md) seamlessly\. 

## Properly filtering Amazon SQS messages<a name="pipes-filter-sqs"></a>

If an Amazon SQS message doesn't satisfy your filter criteria, EventBridge automatically removes the message from the queue\. You don't have to delete these messages manually in Amazon SQS\.

For Amazon SQS, the message `body` can be any string\. However, this can be problematic if your `FilterCriteria` expects `body` to be in a valid JSON format\. The reverse scenario is also true —if the incoming message `body` is in a valid JSON format, but your filter criteria expects `body` to be a plain string, it lead to unintended behavior\.

To avoid this issue, make sure that the format of `body` in your `FilterCriteria` matches the expected format of `body` in messages that you receive from your queue\. Before filtering your messages, EventBridge automatically evaluates the format of the incoming message `body` and of your filter pattern for `body`\. If there is a mismatch, EventBridge drops the message\. The following table summarizes this evaluation:


| Incoming message `body` format | Filter pattern `body` format | Resulting action | 
| --- | --- | --- | 
|  Plain string  |  Plain string  |  EventBridge filters based on your filter criteria\.  | 
|  Plain string  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Plain string  |  Valid JSON  |  EventBridge drops the message\.  | 
|  Valid JSON  |  Plain string  |  EventBridge drops the message\.  | 
|  Valid JSON  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Valid JSON  |  Valid JSON  |  EventBridge filters based on your filter criteria\.  | 

If you don't include `body` as part of your `FilterCriteria`, EventBridge skips this check\.

## Properly filtering Kinesis and DynamoDB messages<a name="pipes-filter-sqs"></a>

After your filter criteria processes a Kinesis or DynamoDB record, the streams iterator advances past this record\. If the record doesn't satisfy your filter criteria, you don't have to delete the record manually from your event source\. After the retention period, Kinesis and DynamoDB automatically delete these old records\. If you want records to be deleted sooner, see [Changing the Data Retention Period](https://docs.aws.amazon.com/kinesis/latest/dev/kinesis-extended-retention.html)\.

To properly filter events from stream event sources, both the data field and your filter criteria for the data field must be in valid JSON format\. \(For Kinesis, the data field is `data`\. For DynamoDB, the data field is `dynamodb`\.\) If either field isn't in a valid JSON format, EventBridge drops the message or throws an exception\. The following table summarizes the specific behavior:


| Incoming data format \(`data` or `dynamodb`\) | Filter pattern format for data properties | Resulting action | 
| --- | --- | --- | 
|  Valid JSON  |  Valid JSON  |  EventBridge filters based on your filter criteria\.  | 
|  Valid JSON  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Valid JSON  |  Non\-JSON  |  EventBridge throws an exception at the time of the pipe or update\. The filter pattern for data properties must be in a valid JSON format\.  | 
|  Non\-JSON  |  Valid JSON  |  EventBridge drops the record\.  | 
|  Non\-JSON  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Non\-JSON  |  Non\-JSON  |  EventBridge throws an exception at the time of the pipe creation or update\. The filter pattern for data properties must be in a valid JSON format\.  | 

## Properly filtering Amazon Managed Streaming for Apache Kafka, self managed Apache Kafka, and Amazon MQ messages<a name="pipes-filter-poller"></a>

**Note**  
After you attach filter criteria to a pipe with an Apache Kafka or Amazon MQ event source, it can take up to 15 minutes to apply your filtering rules to events\.

For [Amazon MQ sources](eb-pipes-mq.md), the message field is `data`\. For Apache Kafka sources \([Amazon MSK](eb-pipes-msk.md) and [self managed Apache Kafka](eb-pipes-kafka.md)\), there are two message fields: `key` and `value`\.

EventBridge drops messages that don't match all fields included in the filter\. For Apache Kafka, EventBridge commits offsets for matched and unmatched messages after successfully invoking the function\. For Amazon MQ, EventBridge acknowledges matched messages after successfully invoking the function and acknowledges unmatched messages when filtering them\.

Apache Kafka and Amazon MQ messages must be UTF\-8 encoded strings, either plain strings or in JSON format\. That's because EventBridge decodes Apache Kafka and Amazon MQ byte arrays into UTF\-8 before applying filter criteria\. If your messages use another encoding, such as UTF\-16 or ASCII, or if the message format doesn't match the `FilterCriteria` format, EventBridge processes metadata filters only\. The following table summarizes the specific behavior:


| Incoming message format \(`data` or `key` and `value`\) | Filter pattern format for message properties | Resulting action | 
| --- | --- | --- | 
|  Plain string  |  Plain string  |  EventBridge filters based on your filter criteria\.  | 
|  Plain string  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Plain string  |  Valid JSON  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Valid JSON  |  Plain string  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Valid JSON  |  No filter pattern for data properties  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 
|  Valid JSON  |  Valid JSON  |  EventBridge filters based on your filter criteria\.  | 
|  Non\-UTF\-8 encoded string  |  JSON, plain string, or no pattern  |  EventBridge filters \(on the other metadata properties only\) based on your filter criteria\.  | 

## Differences between Lambda ESM and EventBridge Pipes<a name="pipes-filter-esm-diff"></a>

When filtering events, Lambda ESM and EventBridge Pipes operate generally the same way\. The main difference is that `eventSourceKey` field isn't present in ESM payloads\.