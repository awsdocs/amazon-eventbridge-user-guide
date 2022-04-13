# Tutorial: Send a notification when an Amazon S3 object is created<a name="eb-s3-object-created-tutorial"></a>

You can send email notifications when [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/Welcome.html) objects are created using Amazon EventBridge and [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)\. In this tutorial, you will create an SNS topic and subscription\. Then, you will create a [rule](eb-rules.md) in the EventBridge console that sends [events](eb-events.md) to that topic when Amazon S3 `Object Created` events are received\.

**Topics**
+ [Prerequisites](#eb-s3-object-created-tutorial-prerequisite)
+ [Step 1: Create an Amazon SNS topic](#eb-s3-object-created-tutorial-create-topic)
+ [Step 2: Create an Amazon SNS subscription](#eb-s3-object-created-tutorial-create-sns)
+ [Step 3: Create a rule](#eb-s3-object-created-tutorial-create-rule)
+ [Step 4: Test the rule](#eb-s3-object-created-tutorial-test-rule)
+ [Step 5: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-s3-object-created-tutorial-prerequisite"></a>

To recieve Amazon S3 events in EventBridge, you must enable EventBridge in the Amazon S3 console\. This tutorial assumes EventBridge is enabled\. For more information, see [Enabling Amazon EventBridge in the S3 console](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/enable-event-notifications-eventbridge.html)\.

## Step 1: Create an Amazon SNS topic<a name="eb-s3-object-created-tutorial-create-topic"></a>

Create a topic to receive the events from EventBridge\.

**To create a topic**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Topics**\.

1. Choose **Create topic**\.

1. For **Type**, choose **Standard**\.

1. Enter **eventbridge\-test** as the name of the topic\.

1. Choose **Create topic**\.

## Step 2: Create an Amazon SNS subscription<a name="eb-s3-object-created-tutorial-create-sns"></a>

Create a subscription to get email notifications from Amazon S3 when events are received by the topic\.

**To create a subscription**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Subscriptions**\.

1. Choose **Create subscription**\.

1. For **Topic ARN**, choose the topic you created in step 1\. For this tutorial, choose **eventbridge\-test**\.

1. For **Protocol**, choose **Email**\.

1. For **Endpoint**, enter your email address\.

1. Choose **Create subscription**\.

1. Confirm the subscription by choosing **Confirm subscription** in the email you receive from AWS notifications\.

## Step 3: Create a rule<a name="eb-s3-object-created-tutorial-create-rule"></a>

Create a rule to send events to your topic when an Amazon S3 object is created\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, name the rule `s3-test`

1. For **Event bus**, select **default**\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **AWS services**\.

1. For **Event pattern**, do the following:

   1. For **Event source**, select **Simple Storage Service \(S3\)** from the drop\-down list\.

   1. For **Event type**, choose ** Amazon S3 Event Notification** from the drop\-down list\.

   1. Choose **Specific events\(s\)** and choose **Object Created** from the drop\-down list\.

   1. Choose **Any bucket**

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **SNS topic** from the drop\-down list\.

1. For **Topic**, select the Kinesis stream that you created in the **Step 1: Create an SNS topic** section\. In this example, select `eventbridge-test`\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 4: Test the rule<a name="eb-s3-object-created-tutorial-test-rule"></a>

To test your rule, create an Amazon S3 object by uploading a file to an EventBridge\-enabled bucket\. Then, wait a few minutes and verify if you receive an email from AWS notifications\.

## Step 5: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the SNS topic**

1. Open the [Topics page](https://console.aws.amazon.com/sns/v3/home#/topics) of the SNS console\.

1. Select the topic that you created\.

1. Choose **Delete**\.

1. Enter **delete me**\.

1. Choose **Delete**\.

**To delete the SNS subscription**

1. Open the [ Subscriptions page](https://console.aws.amazon.com/sns/v3/home#/subscriptions) of the SNS console\.

1. Select the subscription that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.