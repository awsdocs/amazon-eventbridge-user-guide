# Tutorial: Log AWS API calls using EventBridge<a name="eb-log-api-call"></a>

You can use an [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function to log AWS API calls\. For example, you can create a [rule](eb-rules.md) to log any operation in [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html), or you can limit this rule to log only a specific API call\. 

In this tutorial, you create an [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) trail, a Lambda function, and a rule in the EventBridge console\. The rule invokes the Lambda function when an Amazon EC2 instance is stopped\.

**Topics**
+ [Step 1: Create an AWS CloudTrail trail](#eb-log-api-create-ct-trail)
+ [Step 2: Create an AWS Lambda function](#eb-api-create-lambda-function)
+ [Step 3: Create a rule](#eb-api-create-rule)
+ [Step 4: Test the rule](#eb-api-test-rule)
+ [Step 5: Confirm success](#success)
+ [Step 6: Clean up your resources](#cleanup)

## Step 1: Create an AWS CloudTrail trail<a name="eb-log-api-create-ct-trail"></a>

If you already have a trail set up, skip to step 2\.

**To create a trail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Choose **Trails**, **Create trail**\.

1. For **Trail name**, type a name for the trail\.

1. For **Storage location**, in **Create a new S3 bucket**\.

1. For **AWS KMS alias**, type an alias for the KMS key\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Choose **Create trail**\.

## Step 2: Create an AWS Lambda function<a name="eb-api-create-lambda-function"></a>

Create a Lambda function to log the API call events\. 

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name and description for the Lambda function\. For example, name the function `LogEC2StopInstance`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing code with the following code\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogEC2StopInstance');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 3: Create a rule<a name="eb-api-create-rule"></a>

Create a rule to run the Lambda function you created in step 2 whenever you stop an Amazon EC2 instance\.

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

   1. For **Event type**, choose **AWS API Call via CloudTrail**\.

   1. Choose **Specific operations\(s\)** and enter `StopInstances` in the box\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Create**\.

## Step 4: Test the rule<a name="eb-api-test-rule"></a>

You can test your rule by stopping an Amazon EC2 instance using the Amazon EC2 console\. Wait a few minutes for the instance to stop, and then check your AWS Lambda metrics on the CloudWatch console to verify that your function ran\.

**To test your rule by stopping an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Stop the instance\. For more information, see [Stop and Start Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created, and then choose **Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

   1. Select the name of the log stream to view the data provided by the function for the instance that you stopped\.

1. \(Optional\) When you're finished, terminate the stopped instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

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