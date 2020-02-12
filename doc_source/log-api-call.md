# Tutorial: Log AWS API Calls Using EventBridge<a name="log-api-call"></a>

You can use an AWS Lambda function that logs each AWS API call\. For example, you can create a rule to log any operation in Amazon EC2, or you can limit this rule to log only a specific API call\. In this tutorial, you log every time an Amazon EC2 instance is stopped\.

## Prerequisite<a name="log-api-prerequisite"></a>

Before you can match these events, you must use AWS CloudTrail to set up a trail\. If you don't have a trail, complete the following procedure\.

**To create a trail**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Choose **Trails**, **Create trail**\.

1. For **Trail name**, type a name for the trail\.

1. For **Storage location**, in **Create a new S3 bucket** type the name for the new bucket that CloudTrail should deliver logs to\.

1. Choose **Create**\.

## Step 1: Create an AWS Lambda Function<a name="api-create-lambda-function"></a>

Create a Lambda function to log the API call events\. Specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you're new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create a Lambda function**\.

1. On the **Select blueprint** page, enter `hello` for the filter and choose the **hello\-world** blueprint\.

1. On the **Configure triggers** page, choose **Next**\.

1. On the **Configure function** page, do the following:

   1. Enter a name and description for the Lambda function\. For example, name the function `LogEC2StopInstance`\.

   1. Edit the sample code for the Lambda function\. For example:

      ```
      'use strict';
      
      exports.handler = (event, context, callback) => {
          console.log('LogEC2StopInstance');
          console.log('Received event:', JSON.stringify(event, null, 2));
          callback(null, 'Finished');
      };
      ```

   1. For **Role**, choose **Choose an existing role**\. For **Existing role**, select your basic execution role\. Otherwise, create a basic execution role\.

   1. Choose **Next**\.

1. On the **Review** page, choose **Create function**\.

## Step 2: Create a Rule<a name="api-create-rule"></a>

Create a rule to run your Lambda function whenever you stop an Amazon EC2 instance\.

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

   1. By default, the rule matches any Amazon EC2 Auto Scaling group in the Region\. To make the rule match a specific group, choose **Specific group name\(s\)** and select one or more groups\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Targets**, choose **Add target**, **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Create**\.

## Step 3: Test the Rule<a name="api-test-rule"></a>

You can test your rule by stopping an Amazon EC2 instance using the Amazon EC2 console\. After waiting a few minutes for the instance to stop, check your AWS Lambda metrics on the CloudWatch console to verify that your function was invoked\.

**To test your rule by stopping an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Stop the instance\. For more information, see [Stop and Start Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created, and choose **Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

   1. Select the name of the log stream to view the data provided by the function for the instance that you stopped\.

1. \(Optional\) When you're finished, terminate the stopped instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.