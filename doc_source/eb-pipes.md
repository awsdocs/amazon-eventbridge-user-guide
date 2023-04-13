# Amazon EventBridge Pipes<a name="eb-pipes"></a>

## <a name="pipes-overview"></a>

Amazon EventBridge Pipes connects sources to targets\. It reduces the need for specialized knowledge and integration code when developing event driven architectures, fostering consistency across your company’s applications\. To set up a pipe, you choose the source, add optional filtering, define optional enrichment, and choose the target for the event data\.

EventBridge Pipes could be used to create an ecommerce system\. Imagine that you have an API that contains customer metadata, such as shipping addresses\. The source might be an Amazon SQS order received message\. The pipe then sends that data to an Amazon API Gateway API enrichment that returns the customer information for that order\. Finally the pipe sends the enriched data to a AWS Step Functions state machine, which processes the order\.

![\[\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/images/pipes_overview.png)

**Topics**
+ [Source](#pipes-sources)
+ [Filtering](#pipes-filtering)
+ [Enrichment](#pipes-enrichment)
+ [Targets](#pipes-targets)
+ [Validating configuration parameters](#pipes-validation)
+ [Invocation types](#pipes-invocation)
+ [Starting or stopping a pipe](#pipes-start-stop)

### Source<a name="pipes-sources"></a>

EventBridge Pipes receives event data from a variety of sources, applies optional filters and enrichment to that data, and sends it to a target\. If a source enforces order to the events sent to pipes, that order is maintained throughout the entire process to the target\. For more information about sources, see [Amazon EventBridge Pipes sources](eb-pipes-event-source.md)\.

### Filtering<a name="pipes-filtering"></a>

EventBridge Pipes can filter a given source’s events and then process only a subset of those events\. For more information about filtering, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

### Enrichment<a name="pipes-enrichment"></a>

With the enrichment step of EventBridge Pipes, you can enhance the data from the source before sending it to the target\. For example, you might receive *Ticket created* events that don’t include the full ticket data\. Using enrichment, you can have a Lambda function call the `get-ticket` API for the full ticket details\. Pipes can then send that information to a [target](eb-pipes-event-target.md)\.

You can configure the following enrichments when setting up a pipe in EventBridge:
+ API destination
+ Amazon API Gateway
+ Lambda function
+ Step Functions state machine
**Note**  
EventBridge Pipes only supports [Express workflows](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-standard-vs-express.html) as enrichments\.

EventBridge invokes enrichments synchronously because it must wait for a response from the enrichment before invoking the target\.

Enrichment responses are limited to a maximum size of 6MB\.

You can also transform the data you receive from the source before sending it for enhancement\. For more information, see [Amazon EventBridge Pipes input transformation](eb-pipes-input-transformation.md)\.

#### Filtering events using enrichment<a name="pipes-enrichment-filtering"></a>

EventBridge Pipes passes the enrichment responses directly to the configured target\. This includes array responses for targets that support batches\. For more information about batch behavior, see [Amazon EventBridge Pipes batching and concurrency](eb-pipes-batching-concurrency.md)\. You can also use your enrichment as a filter and pass fewer events than were received from the source\. If you don’t want to invoke the target, return an empty response, such as `""`, `{}`, or `[]`\.

**Note**  
If you want to invoke the target with an empty payload, return an array with empty JSON `[{}]`\.

### Targets<a name="pipes-targets"></a>

After the event data has been filtered and enriched, you can send it to a specific target, such as an Amazon Kinesis stream or an Amazon CloudWatch log group\. For a list of the available targets, see [Amazon EventBridge Pipes targets](eb-pipes-event-target.md)\.

You can transform the data after it’s enhanced and before it’s sent to the target\. For more information, see [Amazon EventBridge Pipes input transformation](eb-pipes-input-transformation.md)\.

### Validating configuration parameters<a name="pipes-validation"></a>

After a pipe is created, EventBridge validates the following configuration parameters:
+ **IAM role** – Because the source of a pipe can't be changed after the pipe is created, EventBridge verifies that the provided IAM role can access the source\.
**Note**  
EventBridge doesn't perform the same validation for enrichments or targets because they can be updated after the pipe is created\.
+ **Batching** – EventBridge validates that the batch size of the source doesn't exceed the maximum batch size of the target\. If it does, EventBridge requires a lower batch size\. Additionally, if a target doesn't support batching, you can't configure batching in EventBridge for the source\.
+ **Enrichments** – EventBridge validates that the batch size for API Gateway and API destination enrichments is 1 because only batch sizes of 1 are supported\.

### Invocation types<a name="pipes-invocation"></a>

EventBridge has the following ways to invoke an enrichment or target:
+ **Synchronously** \(invocation type set to `REQUEST_RESPONSE`\) – EventBridge waits for a response from the enrichment or target before proceeding\.
+ **Asynchronously** \(invocation type set to `FIRE_AND_FORGET`\) – EventBridge doesn't wait for a response before proceeding\.

By default, EventBridge invokes enrichments synchronously because it must wait for a response from the enrichment before invoking the target\. Similarly, for pipes with ordered sources, targets are synchronously invoked because a response from the target is needed before proceeding to the next event\. 

If a source doesn't enforce order, such as a standard Amazon SQS queue, EventBridge can invoke a supported target synchronously or asynchronously\. 

With Lambda functions and Step Functions state machines, you can configure the invocation type\.

**Note**  
For Step Functions state machines, [Standard workflows](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-standard-vs-express.html) must be invoked asynchronously\.

### Starting or stopping a pipe<a name="pipes-start-stop"></a>

By default, a pipe is `Running` and processes events when it's created\. To create a pipe without processing events, set the `DesiredState` to `Stopped` in your API call or turn off the **Activate pipe** setting in the console\.

If you create a pipe with Amazon SQS, Kinesis, or DynamoDB sources, pipe creation can typically take a minute or two\.

If you create a pipe with Amazon MSK, self managed Apache Kafka, or Amazon MQ sources, pipes creation can take up to ten minutes\.

To change the state of an existing pipe, do the following:
+ **API** – Set the `DesiredState` parameter to either `RUNNING` or `STOPPED`\.
+ **Console** – On the **Pipes settings** tab, under **Activation**, for **Activate pipe**, turn **Active** on or off\.



There can be a delay between when a pipe is `STOPPED` and when it no longer processes events\. This delay is typically less than two minutes\.
