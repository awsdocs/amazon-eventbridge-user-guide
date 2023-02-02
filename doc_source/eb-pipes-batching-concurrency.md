# Amazon EventBridge Pipes batching and concurrency<a name="eb-pipes-batching-concurrency"></a>

## Batching behavior<a name="pipes-batching"></a>

EventBridge Pipes supports batching from the source and to targets that support it\. In addition, batching to enrichment is supported for AWS Lambda and AWS Step Functions\. Because different services support different levels of batching, you can’t configure a pipe with a larger batch size than the target supports\. For example, Amazon Kinesis stream sources support a maximum batch size of 10,000 records, but Amazon Simple Queue Service supports a maximum of 10 messages per batch as a target\. Therefore, a pipe from a Kinesis stream to an Amazon SQS queue can have a maximum configured batch size on the source of 10\.

If you configure a pipe with an enrichment or target that doesn’t support batching, you won’t be able to activate batching on the source\.

When batching is activated on the source, arrays of JSON records are passed through the pipe and then mapped to the batch API of a supported enrichment or target\. For examples of these arrays, see [Amazon EventBridge Pipes sources](eb-pipes-event-source.md) and select a specific source\. If you do not set a batch size, EventBridge sets the batch size to the default for that source\. If the batch size is one, then the batch will be a JSON array containing a single JSON object\. If the enrichment or target doesn’t have a batch API but receives full JSON payloads, such as Lambda and Step Functions, the entire JSON array is sent in one request\.

If a pipe is configured for batching at the source, and the target supports batching, you can return an array of JSON items from your enrichment\. This array can include a shorter or longer array than the original source\. However, if the array is larger than the batch size supported by the target, the pipe won’t invoke the target\.

### Supported batchable targets<a name="pipes-batchable-target"></a>


| Target | Maximum batch size | 
| --- | --- | 
| CloudWatch Logs | 10,000 | 
| EventBridge event bus | 10 | 
| Kinesis Data Firehose stream | 500 | 
| Kinesis stream | 500 | 
| Lambda function | customer defined | 
| Step Functions state machine | customer defined | 
| Amazon SNS topic | 10 | 
| Amazon SQS queue | 10 | 

The following enrichments and targets receive the full batch event payload for processing and are constrained by the total payload size of the event, rather than the size of the batch:
+ Step Functions state machine \(262144 characters\)
+ Lambda function \(6MB\)

### Partial batch failure<a name="pipes-partial-batch-failure"></a>

For Amazon SQS and stream sources, such as Kinesis and DynamoDB, EventBridge Pipes supports partial batch failure handling of target failures\. If the target supports batching and only part of the batch succeeds, EventBridge automatically retries batching the remainder of the payload\. For the most up\-to\-date enriched content, this retry occurs through the entire pipe, including re\-invoking any configured enrichment\.

Partial batch failure handling of the enrichment is not supported\.

For Lambda, Step Functions, API Gateway, and EventBridge API destination targets, you can also specify a partial failure by returning a payload with defined structure from the target\. This indicates events that need to be retried\. 

**Example partial failure payload structure**

```
{ 
  "batchItemFailures": [ 
    {
      "itemIdentifier": "id2"
    },
    {
      "itemIdentifier": "id4"
    }
]
```

In the example, the `itemIdentifier` must match the ID of the events handled by your target\. For Amazon SQS, this is the `messageId`\. For Kinesis and DynamoDB, this is the `eventID`\. For EventBridge Pipes to adequately handle partial batch failures from the targets, these fields need to be included in any array payload returned by the enrichment\.

## Throughput and concurrency behavior<a name="pipes-concurrency"></a>

Every event or batch of events received by a pipe that travel to an enrichment or target is considered a pipe *execution*\. A pipe in `STARTED` state continuously polls for events from the source, scaling up and down depending on the available backlog and configured batching settings\. By default, all pipes in an account and Region are limited to 1000 concurrent executions\. You can have up to 1000 pipes per account and Region\.

By default, a single pipe will scale to the following maximum concurrent executions, depending on the source:
+ **DynamoDB** – The concurrent executions can climb as high as the `ParallelizationFactor` configured on the pipe multiplied by the number of shards in the stream\.
+ **Apache Kafka** – The concurrent executions can climb as high the number of partitions on the topic\.
+ **Kinesis** – The concurrent execxutions can climb as high as the `ParallelizationFactor` configured on the pipe multiplied by the number of shards in the stream\.
+ **Amazon MQ** – 5
+ **Amazon SQS** – 1250

If you have requirements for higher maximum polling throughputs or concurrency limits, [contact support](https://console.aws.amazon.com/support/home?#/case/create?issueType=technical)\.

**Note**  
The execution limits are considered best\-effort safety limitations\. Although polling isn't throttled below these values, a pipe or account might burst higher than these recommend values\.

Pipe executions are limited to a maximum of 5 minutes including the enrichment and target processing\. This limit currently can't be increased\.

Pipes with strictly ordered sources, such as Amazon SQS FIFO queues, Kinesis and DynamoDB Streams, or Apache Kafka topics\) are further limited in concurrency by the configuration of the source, such as the number of message group IDs for FIFO queues or the number of shards for Kinesis queues\. Because ordering is strictly guaranteedwithin these constraints, a pipe with an ordered source can't exceed those concurrency limits\. 