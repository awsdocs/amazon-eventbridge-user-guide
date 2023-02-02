# Amazon MQ message broker as a source<a name="eb-pipes-mq"></a>

You can use EventBridge Pipes to receive records from an Amazon MQ message broker\. You can then optionally filter or enhance these records before sending them to one of the available destinations for processing\. There are settings specific to Amazon MQ that you can choose when setting up a pipe\. EventBridge Pipes maintains the order of the records from the message broker when sending that data to the destination\.

Amazon MQ is a managed message broker service for [Apache ActiveMQ](https://activemq.apache.org/) and [RabbitMQ](https://www.rabbitmq.com/)\. A message broker enables software applications and components to communicate using different programming languages, operating systems, and formal messaging protocols with either topics or queues as event destinations\.

Amazon MQ can also manage Amazon Elastic Compute Cloud \(Amazon EC2\) instances on your behalf by installing ActiveMQ or RabbitMQ brokers\. After a broker is installed, it provides different network topologies and other infrastructure needs to your instances\.

The Amazon MQ source has the following configuration restrictions:
+ **Cross account** – EventBridge doesn’t support cross\-account processing\. You can’t use EventBridge to process records from an Amazon MQ message broker that is in a different AWS account\.
+ **Authentication** – For ActiveMQ, only the [ ActiveMQ SimpleAuthenticationPlugin](https://activemq.apache.org/security#simple-authentication-plugin) is supported\. For RabbitMQ, only the [PLAIN](https://www.rabbitmq.com/access-control.html#mechanisms) authentication mechanism is supported\. To manage credentials, use AWS Secrets Manager\. For more information about ActiveMQ authentication, see [Integrating ActiveMQ brokers with LDAP](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/security-authentication-authorization.html) in the Amazon MQ Developer Guide\.
+ **Connection quota** – Brokers have a maximum number of allowed connections for each wire\-level protocol\. This quota is based on the broker instance type\. For more information, see the [Brokers](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-limits.html#broker-limits) section of ** \*Quotas in Amazon MQ\*** in the Amazon MQ Developer Guide\.
+ **Connectivity** – You can create brokers in a public or private virtual private cloud \(VPC\)\. For private VPCs, your pipe needs access to the VPC to receive messages\.
+ **Event destinations** – Only queue destinations are supported\. However, you can use a virtual topic, which behaves as both a topic internally and as a queue externally when it interacts with your pipes\. For more information, see [ Virtual Destinations](https://activemq.apache.org/virtual-destinations) on the Apache ActiveMQ website, and [Virtual Hosts](https://www.rabbitmq.com/vhosts.html) on the RabbitMQ website\.
+ **Network topology** – For ActiveMQ, only one single\-instance or standby broker is supported for pipe\. For RabbitMQ, only one single\-instance broker or cluster deployment is supported for each pipe\. Single\-instance brokers require a failover endpoint\. For more information about these broker deployment modes, see [Active MQ Broker Architecture](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-broker-architecture.html) and [Rabbit MQ Broker Architecture](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/rabbitmq-broker-architecture.html) in the Amazon MQ Developer Guide\.
+ **Protocols** – Supported protocols depend on the Amazon MQ integration that you use\.
  + For ActiveMQ integrations, EventBridge uses the OpenWire/Java Message Service \(JMS\) protocol to consume messages\. Message consumption isn’t supported on any other protocol\. EventBridge only supports the [TextMessage](https://activemq.apache.org/maven/apidocs/org/apache/activemq/command/ActiveMQTextMessage.html) and [BytesMessage](https://activemq.apache.org/maven/apidocs/org/apache/activemq/command/ActiveMQBytesMessage.html) operations within the JMS protocol\. For more information about the OpenWire protocol, see [OpenWire](https://activemq.apache.org/openwire.html) on the Apache ActiveMQ website\.
  + For RabbitMQ integrations, EventBridge uses the AMQP 0\-9\-1 protocol to consume messages\. No other protocols are supported for consuming messages\. For more information about RabbitMQ's implementation of the AMQP 0\-9\-1 protocol, see [AMQP 0\-9\-1 Complete Reference Guide](https://www.rabbitmq.com/amqp-0-9-1-reference.html) on the RabbitMQ website\.

EventBridge automatically supports the latest versions of ActiveMQ and RabbitMQ that Amazon MQ supports\. For the latest supported versions, see [Amazon MQ release notes](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/amazon-mq-release-notes.html) in the Amazon MQ Developer Guide\.

**Note**  
By default, Amazon MQ has a weekly maintenance window for brokers\. During that window of time, brokers are unavailable\. For brokers without standby, EventBridge won’t process messages until the window ends\.

**Example events**

The following sample event shows the information that is received by the pipe\. You can use this event to create and filter your event patterns, or to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

**ActiveMQ**

```
[
  {
    "eventSource": "aws:amq",
    "eventSourceArn": "arn:aws:mq:us-west-2:112556298976:broker:test:b-9bcfa592-423a-4942-879d-eb284b418fc8",    
    "messageID": "ID:b-9bcfa592-423a-4942-879d-eb284b418fc8-1.mq.us-west-2.amazonaws.com-37557-1234520418293-4:1:1:1:1",
    "messageType": "jms/text-message",
    "data": "QUJDOkFBQUE=",
    "connectionId": "myJMSCoID",
    "redelivered": false,
    "destination": {
      "physicalname": "testQueue"
    },
    "timestamp": 1598827811958,
    "brokerInTime": 1598827811958,
    "brokerOutTime": 1598827811959
  },
  {
    "eventSource": "aws:amq",
    "eventSourceArn": "arn:aws:mq:us-west-2:112556298976:broker:test:b-9bcfa592-423a-4942-879d-eb284b418fc8",        
    "messageID": "ID:b-9bcfa592-423a-4942-879d-eb284b418fc8-1.mq.us-west-2.amazonaws.com-37557-1234520418293-4:1:1:1:1",
    "messageType": "jms/bytes-message",
    "data": "3DTOOW7crj51prgVLQaGQ82S48k=",
    "connectionId": "myJMSCoID1",
    "persistent": false,
    "destination": {
      "physicalname": "testQueue"
    },
    "timestamp": 1598827811958,
    "brokerInTime": 1598827811958,
    "brokerOutTime": 1598827811959
  }
]
```

**RabbitMQ**

```
[
  {
    "eventSource": "aws:rmq",
    "eventSourceArn": "arn:aws:mq:us-west-2:111122223333:broker:pizzaBroker:b-9bcfa592-423a-4942-879d-eb284b418fc8",
    "eventSourceKey": "pizzaQueue::/",
    "basicProperties": {
      "contentType": "text/plain",
      "contentEncoding": null,
      "headers": {
        "header1": {
          "bytes": [
            118,
            97,
            108,
            117,
            101,
            49
          ]
        },
        "header2": {
          "bytes": [
            118,
            97,
            108,
            117,
            101,
            50
          ]
        },
        "numberInHeader": 10
      },
      "deliveryMode": 1,
      "priority": 34,
      "correlationId": null,
      "replyTo": null,
      "expiration": "60000",
      "messageId": null,
      "timestamp": "Jan 1, 1970, 12:33:41 AM",
      "type": null,
      "userId": "AIDACKCEVSQ6C2EXAMPLE",
      "appId": null,
      "clusterId": null,
      "bodySize": 80
    },
    "redelivered": false,
    "data": "eyJ0aW1lb3V0IjowLCJkYXRhIjoiQ1pybWYwR3c4T3Y0YnFMUXhENEUifQ=="
  }
]
```

## Consumer group<a name="pipes-mq-consumer-group"></a>

To interact with Amazon MQ, EventBridge creates a consumer group that can read from your Amazon MQ brokers\. The consumer group is created with the same ID as the pipe UUID\.

For Amazon MQ sources, EventBridge batches records together and sends them to your function in a single payload\. To control behavior, you can configure the batching window and batch size\. EventBridge pulls messages until one of the following occurs:
+ The processed records reach the payload size maximum of 6 MB\.
+ The batching window expires\.
+ The number of records reaches the full batch size\. 

EventBridge converts your batch into a single payload and then invokes your function\. Messages aren't persisted or deserialized\. Instead, the consumer group retrieves them as a BLOB of bytes\. It then base64\-encodes them into a JSON payload\. If the pipe returns an error for any of the messages in a batch, EventBridge retries the entire batch of messages until processing succeeds or the messages expire\.

## Execution role permissions<a name="pipes-mq-perms"></a>

When setting up a pipe, you can use an existing execution role, or have EventBridge create one for you with the needed permissions\. For Amazon MQ, EventBridge Pipes requires the following permissions to manage resources that are related to your Amazon MQ message broker\. If you’re setting up your own execution role, you must add these permissions yourself\.
+ [https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-http-methods](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/brokers-broker-id.html#brokers-broker-id-http-methods)
+ [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)
+ [https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)
+ [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)
+ [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)
+ [https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

## Network configuration<a name="pipes-mq-vpc-config"></a>

By default, Amazon MQ brokers are created with the `PubliclyAccessible` flag set to false\. It's only when `PubliclyAccessible` is set to true that the broker receives a public IP address\. For full access with your pipe, your broker must either use a public endpoint or provide access to the VPC\.

If your Amazon MQ broker isn't publicly accessible, EventBridge must have access to the Amazon Virtual Private Cloud \(Amazon VPC\) resources associated with your broker\. To access the VPC of your Amazon MQ brokers, EventBridge requires outbound internet access for the subnets of your source\. For public subnets this can be an internet gateway, for private subnets it needs to be a Network Address Translator \(NAT\), such as a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), or your own NAT\. Ensure that the NAT has a public IP address and can connect to the internet\.

Configure your Amazon VPC security groups with the following rules \(at minimum\):
+ Inbound rules – For a broker without public accessibility, allow all traffic on all ports for the security group that's specified as your source\. For a broker with public accessibility, allow all traffic on all ports for all destinations\.
+ Outbound rules – Allow all traffic on all ports for all destinations\.

**Note**  
Your Amazon VPC configuration is discoverable through the [Amazon MQ API](https://docs.aws.amazon.com/amazon-mq/latest/api-reference/resources.html)\. You don't need to configure it during setup\.

## Configuring a pipe with Amazon MQ as the source<a name="pipes-mq-configure"></a>

### Adding the Amazon MQ source<a name="pipes-mq-configure-source"></a>

**To add an Amazon MQ source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **Amazon MQ**\.

1. For **Amazon MQ broker**, choose the stream you want to use as a source\.

1. For **Queue name**, enter the name of the queue that the pipe will read from\.

1. For **Authentication Method**, choose **BASIC\_AUTH**\.

1. For **Secret key**, choose the secret key\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Batch size \- optional**, enter a maximum number of messages for each batch\. The default value is 100\.

   1. For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-mq-configure-filtering"></a>

You can add filtering to your pipe so you’re only sending a subset of records from your Amazon MQ queue to the target\.

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

### \(Optional\) Defining enrichment<a name="pipes-mq-define-enrichment"></a>

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

### Configuring a target<a name="pipes-mq-configure-target"></a>

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

### Configuring the pipe settings<a name="pipes-mq-configure-pipe-settings"></a>

A pipe is active by default, but you can deactivate it\. You can also specify the permissions of the pipe and add tags\.

**To configure the pipe settings**

1. Choose the **Pipe settings** tab\.

1. By default, newly created pipes are active as soon as they're created\. If you want to create an inactive pipe, under **Activation**, for **Activate pipe**, turn off **Active**\.

1. Under **Permissions**, for **Execution role**, do one of the following:

   1. To have EventBridge create a new execution role for this pipe, choose **Create a new role for this specific resource\.** Under **Role name**, you can optionally edit the role name\.

   1. To use an existing execution role, choose **Use existing role**\. Under **Role name**, choose the role\.

1. \(Optional\) Under **Tags \- optional**, choose **Add new tag** and enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create pipe**\.