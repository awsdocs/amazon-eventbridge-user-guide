# Amazon EventBridge Pipes input transformation<a name="eb-pipes-input-transformation"></a>

Amazon EventBridge Pipes support optional input transformers when passing data to the enrichment and the target\. You can use Input transformers to reshape the JSON event input payload to serve the needs of the enrichment or target service\. For Amazon API Gateway and API destinations, this is how you shape the input event to the RESTful model of your API\. Input transformers are modeled as an `InputTemplate` parameter\. They can be free text, a JSON path to the event payload, or a JSON object that includes inline JSON paths to the event payload\. For enrichment, the event payload is coming from the source\. For targets, the event payload is what is returned from the enrichment, if one is configured on the pipe\. In addition to the service\-specific data in the event payload, you can use [reserved variables](#input-transform-reserved) in your `InputTemplate` to reference data for your pipe\.

The following are sample `InputTemplate` parameters referencing an Amazon SQS event payload:

**Static string**

```
InputTemplate: "Hello, sender"
```

**JSON Path**

```
InputTemplate: <$.attributes.SenderId>
```

**Dynamic string**

```
InputTemplate: "Hello, <$.attributes.SenderId>"
```

**Static JSON**

```
InputTemplate: >
{
  "key1": "value1",
  "key2": "value2",
  "key3": "value3",
}
```

**Dynamic JSON**

```
InputTemplate: >
{
  "key1": "value1"
  "key2": <$.body.key>,
  "d": <aws.pipes.event.ingestion-time>
}
```

## Reserved variables<a name="input-transform-reserved"></a>

Input templates can use the following reserved variables:
+ `<aws.pipes.pipe-arn>` – The Amazon Resource Name \(ARN\) of the pipe\.
+ `<aws.pipes.pipe-name>` – The name of the pipe\.
+ `<aws.pipes.source-arn>` – The ARN of the event source of the pipe\.
+ `<aws.pipes.enrichment-arn>` – The ARN of the enrichment of the pipe\.
+ `<aws.pipes.target-arn>` – The ARN of the target of the pipe\.
+ `<aws.pipes.event.ingestion-time>` – The time at which the event was received by the input transformer\. This is an ISO 8601 timestamp\. This time is different for the enrichment input transformer and the target input transformer, depending on when the enrichment completed processing the event\.
+ `<aws.pipes.event>` – The event as received by the input transformer\.

  For an enrichment input transformer, this is the event from the source\. This contains the original payload from the source, plus additional service\-specific metadata\. See the topics in [Amazon EventBridge Pipes sources](eb-pipes-event-source.md) for service\-specific examples\.

  For a target input transformer, this is the event returned by the enrichment, if one is configured, with no additional metadata\. As such, an enrichment\-returned payload may be non\-JSON\. If no enrichment is configured on the pipe, this is the event from the source with metadata\.
+ `<aws.pipes.event.json>` – The same as `aws.pipes.event`, but the variable only has a value if the original payload, either from the source or returned by the enrichment, is JSON\. If the pipe has an encoded field, such as the Amazon SQS `body` field or the Kinesis `data`, those fields are decoded and turned into valid JSON\. Because it isn't escaped, the variable can only be used as a value for a JSON field\. For more information, see [Implicit body data parsing](#input-transform-implicit)\.

## Input transform example<a name="input-transform-example"></a>

The following is an example Amazon EC2 event that we can use as our *sample event*\.

```
{
  "version": "0",
  "id": "7bf73129-1428-4cd3-a780-95db273d1602",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "2015-11-11T21:29:54Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2:us-east-1:123456789012:instance/i-abcd1111"
  ],
  "detail": {
    "instance-id": "i-0123456789",
    "state": "RUNNING"
  }
}
```

Let's use the following JSON as our *Transformer*\.

```
{
  "instance" : <$.detail.instance-id>,
  "state": <$.detail.state>,
  "pipeArn" : <aws.pipes.pipe-arn>,
  "pipeName" : <aws.pipes.pipe-name>,
  "originalEvent" : <aws.pipes.event.json>
}
```

The following will be the resulting *Output*:

```
{
  "instance" : "i-0123456789",
  "state": "RUNNING",
  "pipeArn" : "arn:aws:pipe:us-east-1:123456789012:pipe/example",
  "pipeName" : "example",
  "originalEvent" : {
    ... // commented for brevity
  }
}
```

## Implicit body data parsing<a name="input-transform-implicit"></a>

The following fields in the incoming payload may be JSON\-escaped, such as the Amazon SQS `body` object, or base64\-encoded, such as the Kinesis `data` object\. For both [filtering](eb-pipes-event-filtering.md) and input transformation, EventBridge transforms these fields into valid JSON so sub\-values can be referenced directly\. For example, `<$.data.someKey>` for Kinesis\.

To have the target receive the original payload without any additional metadata, use an input transformer with this body data, specific to the source\. For example, `<$.body>` for Amazon SQS, or `<$.data>` for Kinesis\. If the original payload is a valid JSON string \(for example `{"key": "value"}`\), then use of the input transformer with source specific body data will result in the quotes within the original source payload being removed\. For example, `{"key": "value"}` will become `"{key: value}"` when delivered to the target\. If your target requires valid JSON payloads \(for example, EventBridge Lambda or Step Functions\), this will cause delivery failure\. To have the target receive the original source data without generating invalid JSON, wrap the source body data input transformer in JSON\. For example, `{"data": <$.data>}`\.

**Note**  
If the original payload is valid JSON, this field will contain the unescaped, non\-base64\-encoded JSON\. However, if the payload is not valid JSON, EventBridge base64\-encodes for the fields listed below, with the exception of Amazon SQS\.
+ **Active MQ** – `data`
+ **Kinesis** – `data`
+ **Amazon MSK** – `key` and `value`
+ **Rabbit MQ** – `data`
+ **Self managed Apache Kafka;** – `key` and `value`
+ **Amazon SQS** – `body`