# Tutorial: Log the state of an Amazon EC2 instance using EventBridge<a name="eb-log-ec2-instance-state"></a>

You can create an [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function that logs a state change for an [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) instance\. Then you can create a [rule](eb-rules.md) that runs your Lambda function whenever there is a state transition or a transition to one or more states that are of interest\. In this tutorial, you log the launch of any new instance\.

**Topics**
+ [Step 1: Create an AWS Lambda function](#eb-ec2-create-lambda-function)
+ [Step 2: Create a rule](#eb-ec2-create-rule)
+ [Step 3: Test the rule](#eb-ec2-test-rule)
+ [Step 4: Confirm success](#success)
+ [Step 5: Clean up your resources](#cleanup)

## Step 1: Create an AWS Lambda function<a name="eb-ec2-create-lambda-function"></a>

Create a Lambda function to log the state change [events](eb-events.md)\. When you create your rule in Step 2, you specify this function\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name and description for the Lambda function\. For example, name the function `LogEC2InstanceStateChange`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing code with the following code\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogEC2InstanceStateChange');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 2: Create a rule<a name="eb-ec2-create-rule"></a>

Create a rule to run the Lambda function you created in Step 1\. The rule runs when you launch an Amazon EC2 instance\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **EC2**\.

   1. For **Event type**, choose **EC2 Instance State\-change Notification**\.

   1. Choose **Specific state\(s\)**, **running**\.

   1. By default, the rule matches any instance group in the Region\. To make the rule match a specific instance, choose **Specific instance Id\(s\)** and enter one or more instance IDs\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Target**, choose **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Create**\.

## Step 3: Test the rule<a name="eb-ec2-test-rule"></a>

To test your rule, launch an Amazon EC2 instance\. Wait a few minutes for the instance to launch and initialize, and then verify that your Lambda function ran\.

**To test your rule by launching an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Launch an instance\. For more information, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created, and then choose**Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Choose the name of the log group for your Lambda function, for example `/aws/lambda/function-name`\.

   1. Choose the name of the log stream to view the data provided by the function for the instance that you launched\.

1. \(Optional\) When you're finished, you can open the Amazon EC2 console and stop or terminate the instance that you launched\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 4: Confirm success<a name="success"></a>

If you see the Lambda event in the CloudWatch logs, you've successfully completed this tutorial\. If the event isn't in your CloudWatch logs, start troubleshooting by verifying the rule was created successfully and, if the rule looks correct, verify the code of your Lambda function is correct\.

## Step 5: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.

**To delete the Lambda function**

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console\.

1. Select the function that you created\.

1. Choose **Actions**, **Delete**\.

1. Choose **Delete**\.