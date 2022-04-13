# Tutorial: Log Amazon S3 object\-level operations using EventBridge<a name="eb-log-s3-data-events"></a>

You can log the object\-level API operations on your [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) buckets\. Before Amazon EventBridge can match these [events](eb-events.md), you must use [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) to set up and configure a trail to receive these events\.

In this tutorial, you create CloudTrail trail, create a [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function, and then create [rule](eb-rules.md) in the EventBridge console that invokes that function in response to an S3 data event\.

**Topics**
+ [Step 1: Configure your AWS CloudTrail trail](#eb-configure-trail)
+ [Step 2: Create an AWS Lambda function](#eb-log-s3-create-lambda-function)
+ [Step 3: Create a Rule](#eb-log-s3-create-rule)
+ [Step 4: Test the Rule](#eb-log-s3-test-rule)
+ [Step 5: Confirm success](#success)
+ [Step 6: Clean up your resources](#cleanup)

## Step 1: Configure your AWS CloudTrail trail<a name="eb-configure-trail"></a>

To log data events for an S3 bucket to AWS CloudTrail and EventBridge, you first create a trail\. A *trail* captures API calls and related events in your account and then delivers the log files to an S3 bucket that you specify\. You can update an existing trail or create one\.

For more information, see [Data Events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-and-data-events-with-cloudtrail.html#logging-data-events) in the *AWS CloudTrail User Guide*\. 

**To create a trail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Choose **Trails**, **Create trail**\.

1. For **Trail name**, type a name for the trail\.

1. For **Storage location**, in **Create a new S3 bucket**\.

1. For **AWS KMS alias**, type an alias for the KMS key\.

1. Choose **Next**\.

1. For **Event type**, choose **Data events**

1. For **Data events,** do one of the following:
   + To log data events for all Amazon S3 objects in a bucket, specify an S3 bucket and an empty prefix\. When an event occurs on an object in that bucket, the trail processes and logs the event\.
   + To log data events for specific Amazon S3 objects in a bucket, specify an S3 bucket and the object prefix\. When an event occurs on an object in that bucket and the object starts with the specified prefix, the trail processes and logs the event\.

1. For each resource, choose whether to log **Read** events, **Write** events, or both\.

1. Choose **Next**\.

1. Choose **Create trail**\.

## Step 2: Create an AWS Lambda function<a name="eb-log-s3-create-lambda-function"></a>

Create a Lambda function to log data events for your S3 buckets\. 

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name and description for the Lambda function\. For example, name the function `LogS3DataEvents`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing code with the following code\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogS3DataEvents');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 3: Create a Rule<a name="eb-log-s3-create-rule"></a>

Create a rule to run the Lambda function you created in Step 2\. This rule runs in response to an Amazon S3 data event\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, name the rule `TestRule`

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select **default**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **AWS services**\.

1. For **Event pattern**, do the following:

   1. For **Event source**, select **Simple Storage Service \(S3\)** from the drop\-down list\.

   1. For **Event type**, select **Object\-Level API call via CloudTrail** from the drop\-down list\.

   1. Choose **Specific operation\(s\)**, and then choose **PutObject**\.

   1. By default, the rule matches data events for all buckets in the Region\. To match data events for specific buckets, choose **Specify bucket\(s\) by name** and enter one or more buckets\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **Lambda function** from the drop\-down list\.

1. For **Function**, select the `LogS3DataEvents` Lambda function that you created in step 1\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 4: Test the Rule<a name="eb-log-s3-test-rule"></a>

To test the rule, put an object in your S3 bucket\. You can verify that your Lambda function was invoked\.

**To view the logs for your Lambda function**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

1. Select the name of the log stream to view the data provided by the function for the instance that you launched\.

You can also check your CloudTrail logs in the S3 bucket that you specified for your trail\. For more information, see [Getting and Viewing Your CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/get-and-view-cloudtrail-log-files.html) in the *AWS CloudTrail User Guide*\.

## Step 5: Confirm success<a name="success"></a>

If you see the Lambda event in the CloudWatch logs, you've successfully completed this tutorial\. If the event isn't in your CloudWatch logs, start troubleshooting by verifying the rule was created successfully and, if the rule looks correct, verify the code of your Lambda function is correct\.

## Step 6: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.

**To delete the Lambda function\(s\)**

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console\.

1. Select the function\(s\) that you created\.

1. Choose **Actions**, **Delete**\.

1. Choose **Delete**\.

**To delete the CloudTrail trail\(s\)**

1. Open the [Trails page](https://console.aws.amazon.com/cloudtrail/home#/trails) of the CloudTrail console\.

1. Select the trail\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.