# Amazon EventBridge Pipes sources<a name="eb-pipes-event-source"></a>

EventBridge Pipes receives event data from a variety of sources, applies optional filters and enrichments to that data, and sends it to a destination\.

If a source enforces order to the events sent to EventBridge Pipes, that order is maintained throughout the entire process to the destination\.

The following sources are currently available for EventBridge Pipes:
+ [Amazon DynamoDB stream](eb-pipes-dynamodb.md)
+ [Amazon Kinesis stream](eb-pipes-kinesis.md)
+ [Amazon MQ broker](eb-pipes-mq.md)
+ [Amazon MSK stream ](eb-pipes-msk.md)
+ [Self managed Apache Kafka stream](eb-pipes-kafka.md)
+ [Amazon SQS queue](eb-pipes-sqs.md)