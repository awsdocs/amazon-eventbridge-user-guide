# Self managed Apache Kafka stream as a source<a name="eb-pipes-kafka"></a>

You can use EventBridge Pipes to receive records from self managed Apache Kafka\. You can then optionally filter or enhance these records before sending them to an available destination for processing\. There are source\-specific settings that you can choose when setting up the pipe\. EventBridge Pipes maintains the order of the records received from the cluster when it sends that data to the destination\.

Apache Kafka is an open\-source event streaming platform that supports workloads such as data pipelines and streaming analytics\. You can use the AWS managed Apache Kafka service Amazon Managed Streaming for Apache Kafka \(Amazon MSK\), or a self managed Apache Kafka cluster\.

This topic describes how to use EventBridge Pipes with a self managed Apache Kafka cluster\. In AWS terminology, a self\-managed cluster includes non\-AWS hosted Apache Kafka clusters\. For example, you can host your Apache Kafka cluster with a cloud provider such as [https://www.cloudkarafka.com/](https://www.cloudkarafka.com/)\. You can also use other AWS hosting options for your cluster\. For more information, see [Best Practices for Running Apache Kafka on AWS](http://aws.amazon.com/blogs/big-data/best-practices-for-running-apache-kafka-on-aws/) on the *AWS Big Data Blog***\.

Apache Kafka as an source operates similarly to using Amazon Simple Queue Service \(Amazon SQS\) or Amazon Kinesis\. EventBridge internally polls for new messages from the source and then synchronously invokes the target\. EventBridge reads the messages in batches and provides these to your function as an event payload\. The maximum batch size is configurable\. \(The default is 100 messages\.\)

For Apache Kafka\-based sources, EventBridge supports processing control parameters, such as batching windows and batch size\.

EventBridge sends the batch of messages in the event parameter when it invokes your pipe\. The event payload contains an array of messages\. Each array item contains details of the Apache Kafka topic and Apache Kafka partition identifier, together with a timestamp and a base64\-encoded message\.

**Example events**

The following sample event shows the information that is received by the pipe\. You can use this event to create and filter your event patterns, or to define input transformation\. Not all of the fields can be filtered\. For more information about which fields you can filter, see [Amazon EventBridge Pipes filtering](eb-pipes-event-filtering.md)\.

```
[
  {
    "eventSource": "SelfManagedKafka",
    "bootstrapServers": "b-2.demo-cluster-1.a1bcde.c1.kafka.us-east-1.amazonaws.com:9092,b-1.demo-cluster-1.a1bcde.c1.kafka.us-east-1.amazonaws.com:9092",
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

## Apache Kafka cluster authentication<a name="pipes-smaa-authentication"></a>

EventBridge Pipes supports several methods to authenticate with your self managed Apache Kafka cluster\. Make sure that you configure the Apache Kafka cluster to use one of these supported authentication methods\. For more information about Apache Kafka security, see the [Security](http://kafka.apache.org/documentation.html#security) section of the Apache Kafka documentation\.

### VPC access<a name="pipes-smaa-auth-vpc"></a>

If only Apache Kafka users within your VPC access your Apache Kafka brokers, you must configure the Apache Kafka source for Amazon Virtual Private Cloud \(Amazon VPC\) access\.

### SASL/SCRAM authentication<a name="pipes-smaa-auth-sasl"></a>

EventBridge Pipes supports Simple Authentication and Security Layer/Salted Challenge Response Authentication Mechanism \(SASL/SCRAM\) authentication with Transport Layer Security \(TLS\) encryption\. EventBridge Pipes sends the encrypted credentials to authenticate with the cluster\. For more information about SASL/SCRAM authentication, see [RFC 5802](https://tools.ietf.org/html/rfc5802)\.

EventBridge Pipes supports SASL/PLAIN authentication with TLS encryption\. With SASL/PLAIN authentication, EventBridge Pipes sends credentials as clear text \(unencrypted\) to the server\.

For SASL authentication, you store the user name and password as a secret in AWS Secrets Manager\.

### Mutual TLS authentication<a name="pipes-smaa-auth-mtls"></a>

Mutual TLS \(mTLS\) provides two\-way authentication between the client and server\. The client sends a certificate to the server for the server to verify the client, and the server sends a certificate to the client for the client to verify the server\. 

In self managed Apache Kafka, EventBridge Pipes acts as the client\. You configure a client certificate \(as a secret in Secrets Manager\) to authenticate EventBridge Pipes with your Apache Kafka brokers\. The client certificate must be signed by a certificate authority \(CA\) in the server's trust store\.

The Apache Kafka cluster sends a server certificate to EventBridge Pipes to authenticate the Apache Kafka brokers with EventBridge Pipes\. The server certificate can be a public CA certificate or a private CA/self\-signed certificate\. The public CA certificate must be signed by a CA that's in the EventBridge Pipes trust store\. For a private CA/self\-signed certificate, you configure the server root CA certificate \(as a secret in Secrets Manager\)\. EventBridge Pipes uses the root certificate to verify the Apache Kafka brokers\.

For more information about mTLS, see [ Introducing mutual TLS authentication for Amazon MSK as an source](http://aws.amazon.com/blogs/compute/introducing-mutual-tls-authentication-for-amazon-msk-as-an-event-source)\.

### Configuring the client certificate secret<a name="pipes-smaa-auth-secret"></a>

The CLIENT\_CERTIFICATE\_TLS\_AUTH secret requires a certificate field and a private key field\. For an encrypted private key, the secret requires a private key password\. Both the certificate and private key must be in PEM format\.

**Note**  
EventBridge Pipes supports the [PBES1](https://datatracker.ietf.org/doc/html/rfc2898/#section-6.1) \(but not PBES2\) private key encryption algorithms\.

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

The following example shows the contents of a secret for mTLS authentication using an encrypted private key\. For an encrypted private key, include the private key password in the secret\.

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

### Configuring the server root CA certificate secret<a name="pipes-smaa-auth-ca-cert"></a>

You create this secret if your Apache Kafka brokers use TLS encryption with certificates signed by a private CA\. You can use TLS encryption for VPC, SASL/SCRAM, SASL/PLAIN, or mTLS authentication\.

The server root CA certificate secret requires a field that contains the Apache Kafka broker's root CA certificate in PEM format\. The following example shows the structure of the secret\.

```
{
     "certificate": "-----BEGIN CERTIFICATE-----       
  MIID7zCCAtegAwIBAgIBADANBgkqhkiG9w0BAQsFADCBmDELMAkGA1UEBhMCVVMx
  EDAOBgNVBAgTB0FyaXpvbmExEzARBgNVBAcTClNjb3R0c2RhbGUxJTAjBgNVBAoT
  HFN0YXJmaWVsZCBUZWNobm9sb2dpZXMsIEluYy4xOzA5BgNVBAMTMlN0YXJmaWVs
  ZCBTZXJ2aWNlcyBSb290IENlcnRpZmljYXRlIEF1dG...
  -----END CERTIFICATE-----"
```

## Execution role permissions<a name="pipes-kafka-perms"></a>

When setting up a pipe, you can either use an existing execution role, or have EventBridge create one for you with the needed permissions\. For self managed Apache Kafka, EventBridge requires the following permissions to manage resources that are related to your self managed Apache Kafka stream\. If you’re setting up your own execution role, you must add these permissions yourself\.

### Required permissions<a name="pipes-kafka-perms-req"></a>

To create and store logs in a log group in Amazon CloudWatch Logs, your pipe must have the following permissions in its execution role:
+ [logs:CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)
+ [logs:CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)
+ [logs:PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)

**Note**  
If you’re unsure of the exact well\-scoped permissions required to access the source, use the EventBridge Pipes console to create a new role, then inspect the actions listed in the policy\.

### Optional permissions<a name="pipes-kafka-perms-optional"></a>

Your pipe might also need permissions to:
+ Describe your Secrets Manager secret\.
+ Access your AWS Key Management Service \(AWS KMS\) customer managed key\.
+ Access your Amazon VPC\.

### Secrets Manager and AWS KMS permissions<a name="pipes-kafka-perms-sm-kms"></a>

Depending on the type of access control that you're configuring for your Apache Kafka brokers, your pipe might need permission to access your Secrets Manager secret or to decrypt your AWS KMS customer managed key\. To access these resources, your function's execution role must have the following permissions:
+ [secretsmanager:GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ [kms:Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html)

### VPC permissions<a name="pipes-kafka-perms-vpc"></a>

If only users within a VPC can access your self managed Apache Kafka cluster, your pipe must have permission to access your Amazon VPC resources\. These resources include your VPC, subnets, security groups, and network interfaces\. To access these resources, your pipe's execution role must have the following permissions:
+ [ec2:CreateNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateNetworkInterface.html)
+ [ec2:DescribeNetworkInterfaces](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeNetworkInterfaces.html)
+ [ec2:DescribeVpcs](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVpcs.html)
+ [ec2:DeleteNetworkInterface](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DeleteNetworkInterface.html)
+ [ec2:DescribeSubnets](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSubnets.html)
+ [ec2:DescribeSecurityGroups](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeSecurityGroups.html)

## Network configuration<a name="pipes-kafka-vpc-config"></a>

EventBridge must have access to the Amazon Virtual Private Cloud \(Amazon VPC\) resources associated with your Apache Kafka brokers\. To access the VPC of your Apache Kafka cluster, EventBridge requires outbound internet access for the subnets of your source\. For public subnets this can be an internet gateway, for private subnets it needs to be a Network Address Translator \(NAT\), such as a [NAT gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html), or your own NAT\. Ensure that the NAT has a public IP address and can connect to the internet\.

Configure your Amazon VPC security groups with the following rules \(at minimum\):
+ Inbound rules – Allow all traffic on the Apache Kafka broker port \(9092 for plaintext, 9094 for TLS, 9096 for SASL, 9098 for IAM\) for the security groups specified for your source\.
+ Outbound rules – Allow all traffic on port 443 for all destinations\. Allow all traffic on the Apache Kafka broker port \(9092 for plaintext, 9094 for TLS, 9096 for SASL, 9098 for IAM\) for the security groups specified for your source\.

## Configuring a pipe with self managed Apache Kafka; as the source<a name="pipes-kafka-configure"></a>

### Adding the self managed Apache Kafka stream source<a name="pipes-kafka-configure-source"></a>

**To add a self managed Apache Kafka source by using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. On the navigation pane, choose **Pipes**\.

1. Choose **Create pipe**\.

1. Enter a name for the pipe\.

1. \(Optional\) Add a description for the pipe\.

1. On the **Build pipe** tab, for **Source**, choose **Self\-managed Apache Kafka**\.

1. For **Bootstrap servers**, enter the `host:port` pair addresses of your brokers\.

1. For **Topic name**, enter the name of topic that the pipe will read from\.

1. \(Optional\) For **VPC**, choose the VPC that you want\. Then, for **VPC subnets**, choose the desired subnets\. For **VPC security groups**, choose the security groups\.

1. \(Optional\) For **Authentication \- optional**, turn on **Use Authentication** and do the following:

   1. For **Authentication method**, choose the authentication type\.

   1. For **Secret key**, choose the secret key\.

1. \(Optional\) For **Additional setting \- optional**, do the following:

   1. For **Starting position**, choose one of the following:
      + **Latest** – Start reading the stream with the most recent record in the shard\.
      + **Trim horizon** – Start reading the stream with the last untrimmed record in the shard\. This is the oldest record in the shard\.

   1. For **Batch size \- optional**, enter a maximum number of records for each batch\. The default value is 100\.

   1. For **Batch window \- optional**, enter a maximum number of seconds to gather records before proceeding\.

Now that the source is configured, you can add optional filtering, optional enrichment, or a target for the pipe\.

### \(Optional\) Configuring filtering<a name="pipes-kafka-configure-filtering"></a>

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

### \(Optional\) Defining enrichment<a name="pipes-kafka-define-enrichment"></a>

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

### Configuring a target<a name="pipes-kafka-configure-target"></a>

**To configure a target**

1. Choose **Target**\.

1. Under **Details**, for **Target service**, choose the target that you want\. The fields that display vary depending on the target that you choose\. Enter information specific to this target type, as needed\.

You can also transform the data before sending it to the target\.

**\(Optional\) To define the input transformer**

1. Choose **Target Input Transformer \- optional**\.

1. For **Sample events/Event Payload**, choose the sample event type\.

1. For **Transformer**, enter the transformer syntax, such as `"Event happened at <$.detail.field>."` where `<$.detail.field>` is a reference to a field from the sample event\. You can also double\-click a field from the sample event to add it to the transformer\.

1. For **Output**, verify that the output looks like you want it to\.

Now that the pipe is configured, make sure that its settings are configured correctly\.

### Configuring the pipe settings<a name="pipes-kafka-configure-pipe-settings"></a>

A pipe is active by default, but you can deactivate it\. You can also specify the permissions of the pipe and add tags\.

**To configure the pipe settings**

1. Choose the **Pipe settings** tab\.

1. By default, newly created pipes are active as soon as they're created\. If you want to create an inactive pipe, under **Activation**, for **Activate pipe**, turn off **Active**\.

1. Under **Permissions**, for **Execution role**, do one of the following:

   1. To have EventBridge create a new execution role for this pipe, choose **Create a new role for this specific resource\.** Under **Role name**, you can optionally edit the role name\.

   1. To use an existing execution role, choose **Use existing role**\. Under **Role name**, choose the role\.

1. \(Optional\) Under **Tags \- optional**, choose **Add new tag** and enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create pipe**\.

## Auto scaling of the Apache Kafka source<a name="pipes-kafka-ops-scaling"></a>

When you initially create an Apache Kafka source, EventBridge allocates one consumer to process all partitions in the Kafka topic\. Each consumer has multiple processors running in parallel to handle increased workloads\. Additionally, EventBridge automatically scales up or down the number of consumers, based on workload\. To preserve message ordering in each partition, the maximum number of consumers is one consumer per partition in the topic\.

In one\-minute intervals, EventBridge evaluates the consumer offset lag of all the partitions in the topic\. If the lag is too high, the partition is receiving messages faster than EventBridge can process them\. If necessary, EventBridge adds or removes consumers from the topic\. The scaling process of adding or removing consumers occurs within three minutes of evaluation\.

If your target is overloaded, EventBridge reduces the number of consumers\. This action reduces the workload on the function by reducing the number of messages that consumers can retrieve and send to the function\.