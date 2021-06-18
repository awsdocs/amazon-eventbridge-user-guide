# Tutorial: Log the state of an Auto Scaling group using EventBridge<a name="eb-log-as-group-state"></a>

You can run an [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function that logs an [events](eb-events.md) whenever an Auto Scaling group launches or terminates an Amazon EC2 instance that indicates whether an event was successful\.

For information about more scenarios that use Amazon EC2 Auto Scaling events, see [Getting CloudWatch Events When Your Auto Scaling Group Scales](https://docs.aws.amazon.com/autoscaling/latest/userguide/cloud-watch-events.html) in the *Amazon EC2 Auto Scaling User Guide*\.

In this tutorial, you create a Lambda function, and you create a [rule](eb-rules.md) in the EventBridge console that calls that function when an Auto Scaling group launches or terminates an instance\.

**Topics**
+ [Step 1: Create a Lambda function](#eb-as-create-lambda-function)
+ [Step 2: Create a rule](#eb-as-create-rule)
+ [Step 3: Test the rule](#eb-as-test-rule)
+ [Step 4: Confirm success](#success)
+ [Step 5: Clean up your resources](#cleanup)

## Step 1: Create a Lambda function<a name="eb-as-create-lambda-function"></a>

Create a Lambda function to log the scale\-out and scale\-in events for your Auto Scaling group\. 

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name for the Lambda function\. For example, name the function `LogAutoScalingEvent`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing code with the following code\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogAutoScalingEvent');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 2: Create a rule<a name="eb-as-create-rule"></a>

Create a rule to run the Lambda function you created in Step 1\. The rule runs when your Auto Scaling group starts or stops an instance\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event Pattern**\.

   1. Choose **Pre\-defined by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **Auto Scaling**\.

   1. For **Event type**, choose **Instance Launch and Terminate**\.

   1. To capture all successful and unsuccessful instance launch and terminate events, choose **Any instance event**\.

   1. By default, the rule matches any Auto Scaling group in the Region\. To make the rule match a specific Auto Scaling group, choose **Specific group name\(s\)**, and then select one or more Auto Scaling groups\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Target**, choose **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Create**\.

## Step 3: Test the rule<a name="eb-as-test-rule"></a>

You can test your rule by manually scaling an Auto Scaling group so that it launches an instance\. Wait a few minutes for the scale\-out event to occur, and then verify that your Lambda function was invoked\.

**To test your rule using an Auto Scaling group**

1. To increase the size of your Auto Scaling group, do the following:

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Auto Scaling**, **Auto Scaling Groups**\.

   1. Select the check box for your Auto Scaling group\.

   1. On the **Details** tab, choose **Edit**\. For **Desired**, increase the desired capacity by one\. For example, if the current value is **2**, enter **3**\. The desired capacity must be less than or equal to the maximum size of the group\. If your new value for **Desired** is greater than **Max**, you must update **Max**\. When you're finished, choose **Save**\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created, and then choose **Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

   1. Select the name of the log stream to view the data provided by the function for the instance that you launched\.

1. \(Optional\) When you're finished, you can decrease the desired capacity by one so that the Auto Scaling group returns to its previous size\.

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