# Amazon Managed Streaming for Apache Kafka topic as a source<a name="eb-pipes-msk"></a>

You can use EventBridge Pipes to receive records from an [Amazon Managed Streaming for Apache Kafka \(Amazon MSK\)](https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html) topic\. You can optionally filter or enhance these records before sending them to one of the available destinations for processing\. There are settings specific to Amazon MSK that you can choose when setting up a pipe\. EventBridge Pipes maintains the order of the records from the message broker when sending that data to the destination\.

Amazon MSK is a fully managed service that you can use to build and run applications that use Apache Kafka to process streaming data\. Amazon MSK simplifies the setup, scaling, and management of clusters running Apache Kafka\. With Amazon MSK, you can configure your application for multiple Availability Zones and for security with AWS Identity and Access Management \(IAM\)\. Amazon MSK supports multiple open\-source versions of Kafka\.

Amazon MSK as an source operates similarly to using Amazon Simple Queue Service \(Amazon SQS\) or Amazon Kinesis\. EventBridge internally polls for new messages from the source and then synchronously invokes the target\. EventBridge reads the messages in batches and provides these to your function as an event payload\. The maximum batch size is configurable\. \(The default is 100 messages\.\)

For Apache Kafka\-based sources, EventBridge supports processing control parameters, such as batching windows and batch size\.

EventBridge reads the messages sequentially for each partition\. After EventBridge processes each batch, it commits the offsets of the messages in that batch\. If the pipe's target returns an error for any of the messages in a batch, EventBridge retries the entire batch of messages until processing succeeds or the messages expire\.

EventBridge sends the batch of messages in the event when it invokes the target\. The event payload contains an array of messages\. Each array item contains details of the Amazon MSK topic and partition identifier, together with a timestamp and a base64\-encoded message\.

**Example events**

The following sample event shows the information that is received by the pipe\. You can use this event to create and filter your event patterns, or for to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

```
[
  {
    "eventSource": "aws:kafka",
    "eventSourceArn": "arn:aws:kafka:sa-east-1:123456789012:cluster/vpc-2priv-2pub/751d2973-a626-431c-9d4e-d7975eb44dd7-2",    
    "eventSourceKey": "mytopic-0",
    "topic": "mytopic",
    "partition": "0",
    "offset": 15,
    "timestamp": 1545084650987,
    "timestampType": "CREATE_TIME",
    "key":"abcDEFghiJKLmnoPQRstuVWXyz1234==", 
    "value":"SGVsbG8sIHRoaXMgaXMgYSB0ZXN0Lg==",
    "headers": [
      {
        "headerKey": [
          104,
          101,
          97,
          100,
          101,
          114,
          86,
          97,
          108,
          117,
          101
        ]
      }
    ]
  }
]
```

## MSK cluster authentication<a name="pipes-msk-cluster-permissions"></a>

EventBridge needs permission to access the Amazon MSK cluster, retrieve records, and perform other tasks\. Amazon MSK supports several options for controlling client access to the MSK cluster\. For more information about which authentication method is used when, see [How EventBridge chooses a bootstrap broker](#pipes-msk-bootstrap-brokers)\.

**Topics**
+ [Unauthenticated access](#pipes-msk-permissions-none)
+ [SASL/SCRAM authentication](#pipes-msk-permissions-add-secret)
+ [IAM role\-based authentication](#pipes-msk-permissions-iam-policy)
+ [Mutual TLS authentication](#pipes-msk-permissions-mTLS)
+ [Configuring the mTLS secret](#smaa-auth-secret)
+ [How EventBridge chooses a bootstrap broker](#pipes-msk-bootstrap-brokers)

### Unauthenticated access<a name="pipes-msk-permissions-none"></a>

We recommend only using unauthenticated access for development\. Unauthenticated access will only work if IAM role\-based authentication is disabled for the cluster\.

### SASL/SCRAM authentication<a name="pipes-msk-permissions-add-secret"></a>

Amazon MSK supports Simple Authentication and Security Layer/Salted Challenge Response Authentication Mechanism \(SASL/SCRAM\) authentication with Transport Layer Security \(TLS\) encryption\. For EventBridge to connect to the cluster, you store the authentication credentials \(user name and password\) in an AWS Secrets Manager secret\.

For more information about using Secrets Manager, see [User name and password authentication with AWS Secrets Manager](https://docs.aws.amazon.com/msk/latest/developerguide/msk-password.html) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

Amazon MSK doesn't support SASL/PLAIN authentication\.

### IAM role\-based authentication<a name="pipes-msk-permissions-iam-policy"></a>

You can use IAM to authenticate the identity of clients that connect to the MSK cluster\. If IAM authentication is active on your MSK cluster, and you don't provide a secret for authentication, EventBridge automatically defaults to using IAM authentication\. To create and deploy IAM user or role\-based policies, use the IAM console or API\. For more information, see [IAM access control](https://docs.aws.amazon.com/msk/latest/developerguide/iam-access-control.html) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

To allow EventBridge to connect to the MSK cluster, read records, and perform other required actions, add the following permissions to your pipes's execution role\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kafka-cluster:Connect",
                "kafka-cluster:DescribeGroup",
                "kafka-cluster:AlterGroup",
                "kafka-cluster:DescribeTopic",
                "kafka-cluster:ReadData",
                "kafka-cluster:DescribeClusterDynamicConfiguration"
            ],
            "Resource": [
                "arn:aws:kafka:region:account-id:cluster/cluster-name/cluster-uuid",
                "arn:aws:kafka:region:account-id:topic/cluster-name/cluster-uuid/topic-name",
                "arn:aws:kafka:region:account-id:group/cluster-name/cluster-uuid/consumer-group-id"
            ]
        }
    ]
}
```

You can scope these permissions to a specific cluster, topic, and group\. For more information, see the [Amazon MSK Kafka actions](https://docs.aws.amazon.com/msk/latest/developerguide/iam-access-control.html#kafka-actions) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

### Mutual TLS authentication<a name="pipes-msk-permissions-mTLS"></a>

Mutual TLS \(mTLS\) provides two\-way authentication between the client and server\. The client sends a certificate to the server for the server to verify the client, and the server sends a certificate to the client for the client to verify the server\. 

For Amazon MSK, EventBridge acts as the client\. You configure a client certificate \(as a secret in Secrets Manager\) to authenticate EventBridge with the brokers in your MSK cluster\. The client certificate must be signed by a certificate authority \(CA\) in the server's trust store\. The MSK cluster sends a server certificate to EventBridge to authenticate the brokers with EventBridge\. The server certificate must be signed by a CA that's in the AWS trust store\. 

Amazon MSK doesn't support self\-signed server certificates, because all brokers in Amazon MSK use [public certificates](https://docs.aws.amazon.com/msk/latest/developerguide/msk-encryption.html) signed by [Amazon Trust Services CAs](https://www.amazontrust.com/repository/), which EventBridge trusts by default\.



For more information about mTLS for Amazon MSK, see [Mutual TLS Authentication](https://docs.aws.amazon.com/msk/latest/developerguide/msk-authentication.html) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

### Configuring the mTLS secret<a name="smaa-auth-secret"></a>

The CLIENT\_CERTIFICATE\_TLS\_AUTH secret requires a certificate field and a private key field\. For an encrypted private key, the secret requires a private key password\. Both the certificate and private key must be in PEM format\.

**Note**  
EventBridge supports the [PBES1](https://datatracker.ietf.org/doc/html/rfc2898/#section-6.1) \(but not PBES2\) private key encryption algorithms\.

The certificate field must contain a list of certificates, beginning with the client certificate, followed by any intermediate certificates, and ending with the root certificate\. Each certificate must start on a new line with the following structure:

```
-----BEGIN CERTIFICATE-----  
        <certificate contents>
-----END CERTIFICATE-----
```

Secrets Manager supports secrets up to 65,536 bytes, which is enough space for long certificate chains\.

The private key must be in [PKCS \#8](https://datatracker.ietf.org/doc/html/rfc5208) format, with the following structure:

```
-----BEGIN PRIVATE KEY-----  
         <private key contents>
-----END PRIVATE KEY-----
```

For an encrypted private key, use the following structure:

```
-----BEGIN ENCRYPTED PRIVATE KEY-----  
          <private key contents>
-----END ENCRYPTED PRIVATE KEY-----
```

The following example shows the contents of a secret for mTLS authentication using an encrypted private key\. For an encrypted private key, you include the private key password in the secret\.

```
{
 "privateKeyPassword": "testpassword",
 "certificate": "-----BEGIN CERTIFICATE-----
MIIE5DCCAsygAwIBAgIRAPJdwaFaNRrytHBto0j5BA0wDQYJKoZIhvcNAQELBQAw
...
j0Lh4/+1HfgyE2KlmII36dg4IMzNjAFEBZiCRoPimO40s1cRqtFHXoal0QQbIlxk
cmUuiAii9R0=
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIFgjCCA2qgAwIBAgIQdjNZd6uFf9hbNC5RdfmHrzANBgkqhkiG9w0BAQsFADBb
...
rQoiowbbk5wXCheYSANQIfTZ6weQTgiCHCCbuuMKNVS95FkXm0vqVD/YpXKwA/no
c8PH3PSoAaRwMMgOSA2ALJvbRz8mpg==
-----END CERTIFICATE-----",
 "privateKey": "-----BEGIN ENCRYPTED PRIVATE KEY-----
MIIFKzBVBgkqhkiG9w0BBQ0wSDAnBgkqhkiG9w0BBQwwGgQUiAFcK5hT/X7Kjmgp
...
QrSekqF+kWzmB6nAfSzgO9IaoAaytLvNgGTckWeUkWn/V0Ck+LdGUXzAC4RxZnoQ
zp2mwJn2NYB7AZ7+imp0azDZb+8YG2aUCiyqb6PnnA==
-----END ENCRYPTED PRIVATE KEY-----"
}
```

### How EventBridge chooses a bootstrap broker<a name="pipes-msk-bootstrap-brokers"></a>

EventBridge chooses a [ bootstrap broker](https://docs.aws.amazon.com/msk/latest/developerguide/msk-get-bootstrap-brokers.html) based on the authentication methods available on your cluster, and whether you provide a secret for authentication\. If you provide a secret for mTLS or SASL/SCRAM, EventBridge automatically chooses that authentication method\. If you don't provide a secret, EventBridge chooses the strongest authentication method that's active on your cluster\. The following is the order of priority in which EventBridge selects a broker, from strongest to weakest authentication:
+ mTLS \(secret provided for mTLS\)
+ SASL/SCRAM \(secret provided for SASL/SCRAM\)
+ SASL IAM \(no secret provided, and IAM authentication is active\)
+ Unauthenticated TLS \(no secret provided, and IAM authentication is not active\)
+ Plaintext \(no secret provided, and both IAM authentication and unauthenticated TLS are not active\)

**Note**  
If EventBridge can't connect to the most secure broker type, it doesn't attempt to connect to a different \(weaker\) broker type\. If you want EventBridge to choose a weaker broker type, deactivate all stronger authentication methods on your cluster\.

## Execution role permissions<a name="pipes-msk-perms"></a>

When setting up a pipe, you can use an existing execution role, or have EventBridge create one for you with the needed permissions\. For Amazon MSK, EventBridge requires the following permissions to manage resources that are related to your Amazon MSK topic\. If you’re setting up your own execution role, you must add these permissions yourself\.

**Note**  
If you're using IAM role\-based authentication, your execution role will need the permissions listed in [ IAM role\-based authentication](#pipes-msk-permissions-iam-policy) in addition the ones listed below\.
+ [kafka:DescribeClusterV2](https://docs.aws.amazon.com/MSK/2.0/APIReference/v2-clusters-clusterarn.html#v2-clusters-clusterarnget)
+ [kafka:GetBootstrapBrokers](https://docs.aws.amazon.com/msk/1.0/apireference/clusters-clusterarn-bootstrap-brokers.html#clusters-clusterarn-bootstrap-brokersget)
+ [ec2:CreateNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)
+ [ec2:DescribeNetworkInterfaces](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)
+ [ec2:DescribeVpcs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)
+ [ec2:DeleteNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)
+ [ec2:DescribeSubnets](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)
+ [ec2:DescribeSecurityGroups](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)
+ [logs:CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)
+ [logs:CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)
+ [logs:PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

## Network configuration<a name="pipes-msk-vpc-config"></a>

EventBridge must have access to the Amazon Virtual Private Cloud \(Amazon VPC\) resources associated with your Amazon MSK cluster\. To access the VPC of your Amazon MSK cluster, EventBridge requires outbound internet access for the subnets of your source\. For public subnets this can be an internet gateway, for private subnets it needs to be a Network Address Translator \(NAT\), such as a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), or your own NAT\. Ensure that the NAT has a public IP address and can connect to the internet\.

Configure your Amazon VPC security groups with the following rules \(at minimum\):
+ Inbound rules – Allow all traffic on the Amazon MSK broker port \(9092 for plaintext, 9094 for TLS, 9096 for SASL, 9098 for IAM\) for the security groups specified for your source\.
+ Outbound rules – Allow all traffic on port 443 for all destinations\. Allow all traffic on the Amazon MSK broker port \(9092 for plaintext, 9094 for TLS, 9096 for SASL, 9098 for IAM\) for the security groups specified for your source\.

**Note**  
Your Amazon VPC configuration is discoverable through the [Amazon MSK API](https://docs.aws.amazon.com/msk/1.0/apireference/resources.html)\. You don't need to configure it during setup\.

## Customizable consumer group ID<a name="pipes-msk-consumer-group"></a>

When setting up Apache Kafka as an source, you can specify a consumer group ID\. This consumer group ID is an existing identifier for the Apache Kafka consumer group that you want your pipe to join\. You can use this feature to migrate any ongoing Apache Kafka record processing setups from other consumers to EventBridge\.

If you specify a consumer group ID and there are other active pollers within that consumer group, Apache Kafka distributes messages across all consumers\. In other words, EventBridge doesn't receive all messages for the Apache Kafka topic\. If you want EventBridge to handle all messages in the topic, turn off any other pollers in that consumer group\.

Additionally, if you specify a consumer group ID, and Apache Kafka finds a valid existing consumer group with the same ID, EventBridge ignores the `StartingPosition` parameter for your pipe\. Instead, EventBridge begins processing records according to the committed offset of the consumer group\. If you specify a consumer group ID, and Apache Kafka can't find an existing consumer group, then EventBridge configures your source with the specified `StartingPosition`\.

The consumer group ID that you specify must be unique among all your Apache Kafka event sources\. After creating a pipe with the consumer group ID specified, you can't update this value\.

## Configuring a pipe with Amazon MSK as the source<a name="pipes-msk-configure"></a>

### Adding the Amazon MSK topic source<a name="pipes-msk-configure-source"></a>

**To add an Amazon MSK source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **Amazon MSK**\.

1. For **Amazon MSK cluster**, choose the cluster that you want to use\.

1. For **Topic name**, enter the name of topic that the pipe will read from\.

1. \(Optional\) For **Consumer Group ID \- optional**, enter the ID of the consumer group you want the pipe to join\.

1. \(Optional\) For **Authentication \- optional**, turn on **Use Authentication** and do the following:

   1. For **Authentication method**, choose the type you want\.

   1. For **Secret key**, choose the secret key\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Batch size \- optional**, enter a maximum number of records for each batch\. The default value is 100\.

   1. For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

   1. For **Starting position**, choose one of the following:
      + **Latest** – Start reading the topic with the most recent record in the shard\.
      + **Trim horizon** – Start reading the topic with the last untrimmed record in the shard\. This is the oldest record in the shard\.
**Note**  
**Trim horizon** is the same as **Earliest** for Apache Kafka\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-msk-configure-filtering"></a>

You can add filtering to your pipe so you’re only sending a subset of records from your Amazon MSK queue to the target\.

**To configure filtering by using the console**

1. Choose **Filtering**\.

1. Under **Sample event \- optional**, you’ll see a sample Amazon MSK event you can use to build your event pattern, or you can enter your own event by choosing **Enter your own**\.

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

### \(Optional\) Defining enrichment<a name="pipes-msk-define-enrichment"></a>

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

### Configuring a target<a name="pipes-msk-configure-target"></a>

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

### Configuring the pipe settings<a name="pipes-msk-configure-pipe-settings"></a>

A pipe is active by default, but you can deactivate it\. You can also specify the permissions of the pipe and add tags\.

**To configure the pipe settings**

1. Choose the **Pipe settings** tab\.

1. By default, newly created pipes are active as soon as they're created\. If you want to create an inactive pipe, under **Activation**, for **Activate pipe**, turn off **Active**\.

1. Under **Permissions**, for **Execution role**, do one of the following:

   1. To have EventBridge create a new execution role for this pipe, choose **Create a new role for this specific resource\.** Under **Role name**, you can optionally edit the role name\.

   1. To use an existing execution role, choose **Use existing role**\. Under **Role name**, choose the role\.

1. \(Optional\) Under **Tags \- optional**, choose **Add new tag** and enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create pipe**\.

## Auto scaling of the Amazon MSK source<a name="pipes-msk-ops-scaling"></a>

When you initially create an Amazon MSK source, EventBridge allocates one consumer to process all partitions in the Apache Kafka topic\. Each consumer has multiple processors running in parallel to handle increased workloads\. Additionally, EventBridge automatically scales up or down the number of consumers, based on workload\. To preserve message ordering in each partition, the maximum number of consumers is one consumer per partition in the topic\.

In one\-minute intervals, EventBridge evaluates the consumer offset lag of all the partitions in the topic\. If the lag is too high, the partition is receiving messages faster than EventBridge can process them\. If necessary, EventBridge adds or removes consumers from the topic\. The scaling process of adding or removing consumers occurs within three minutes of evaluation\.

If your target is overloaded, EventBridge reduces the number of consumers\. This action reduces the workload on the pipe by reducing the number of messages that consumers can retrieve and send to the pipe\.