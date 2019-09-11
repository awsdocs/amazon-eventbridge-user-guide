# Tutorial: Relay Events to an Amazon Kinesis Stream Using EventBridge<a name="relay-events-kinesis-stream"></a>

You can relay AWSAWS API call events in EventBridge to a stream in Amazon Kinesis\.

## Prerequisite<a name="stream-prerequisite"></a>

Install the AWS CLI\. For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

## Step 1: Create an Amazon Kinesis Stream<a name="stream-create-stream"></a>

To create a stream, use the following [create\-stream](https://docs.aws.amazon.com/cli/latest/reference/kinesis/create-stream.html) command\.

```
aws kinesis create-stream --stream-name test --shard-count 1
```

When the stream status is `ACTIVE`, the stream is ready\. To check the stream status, use the following [describe\-stream](https://docs.aws.amazon.com/cli/latest/reference/kinesis/describe-stream.html) command\.

```
aws kinesis describe-stream --stream-name test
```

## Step 2: Create a Rule<a name="stream-create-rule"></a>

As an example, create a rule to send events to your stream when you stop an Amazon EC2 instance\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **EC2**\.

   1. For **Event type**, choose **Instance State\-change Notification**\.

   1. Choose **Specific state\(s\)**, **running**\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Targets**, choose **Kinesis stream**\.

1. For **Stream**, select the stream that you created\.

1. Choose **Create a new role for this specific resource**\.

1. Choose **Create**\.

## Step 3: Test the Rule<a name="stream-test-rule"></a>

To test your rule, stop an Amazon EC2 instance\. After waiting a few minutes for the instance to stop, check your CloudWatch metrics to verify that your function was invoked\.

**To test your rule by stopping an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

   Choose the name of the rule that you created and choose **Metrics for the rule**\.

1. \(Optional\) When you're finished, terminate the instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 4: Verify That the Event Is Relayed<a name="stream-verify-event"></a>

You can get the record from the stream to verify that the event was relayed\.

**To get the record**

1. To start reading from your Kinesis stream, use the following [get\-shard\-iterator](https://docs.aws.amazon.com/cli/latest/reference/kinesis/get-shard-iterator.html) command\.

   ```
   aws kinesis get-shard-iterator --shard-id shardId-000000000000 --shard-iterator-type TRIM_HORIZON --stream-name test
   ```

   The following is example output\.

   ```
   {
       "ShardIterator": "AAAAAAAAAAHSywljv0zEgPX4NyKdZ5wryMzP9yALs8NeKbUjp1IxtZs1Sp+KEd9I6AJ9ZG4lNR1EMi+9Md/nHvtLyxpfhEzYvkTZ4D9DQVz/mBYWRO6OTZRKnW9gd+efGN2aHFdkH1rJl4BL9Wyrk+ghYG22D2T1Da2EyNSH1+LAbK33gQweTJADBdyMwlo5r6PqcP2dzhg="
   }
   ```

1. To get the record, use the following `get-records` command\. The shard iterator is the one that you got in the previous step\.

   ```
   aws kinesis get-records --shard-iterator AAAAAAAAAAHSywljv0zEgPX4NyKdZ5wryMzP9yALs8NeKbUjp1IxtZs1Sp+KEd9I6AJ9ZG4lNR1EMi+9Md/nHvtLyxpfhEzYvkTZ4D9DQVz/mBYWRO6OTZRKnW9gd+efGN2aHFdkH1rJl4BL9Wyrk+ghYG22D2T1Da2EyNSH1+LAbK33gQweTJADBdyMwlo5r6PqcP2dzhg=
   ```

   If the command is successful, it requests records from your stream for the specified shard\. You can receive zero or more records\. Any records returned might not represent all records in your stream\. If you don't receive the data that you expect, keep calling `get-records`\.

   Records in Kinesis are Base64\-encoded\. However, the streams support in the AWS CLI doesn't provide Base64 decoding\. If you use a Base64 decoder to manually decode the data, you see that it's the event relayed to the stream in JSON form\.