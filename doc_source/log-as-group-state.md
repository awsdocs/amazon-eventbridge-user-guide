# Tutorial: Log the State of an Auto Scaling Group Using EventBridge<a name="log-as-group-state"></a>

You can run an AWS Lambda function that logs an event whenever an Auto Scaling group launches or terminates an Amazon EC2 instance and whether the launch or terminate event was successful\.

For information about additional EventBridge scenarios using Amazon EC2 Auto Scaling events, see [Getting CloudWatch Events When Your Auto Scaling Group Scales](https://docs.aws.amazon.com/autoscaling/latest/userguide/cloud-watch-events.html) in the *Amazon EC2 Auto Scaling User Guide*\.

## Step 1: Create an AWS Lambda Function<a name="as-create-lambda-function"></a>

Create a Lambda function to log the scale\-out and scale\-in events for your Auto Scaling group\. Specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you're new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create a Lambda function**\.

1. On the **Select blueprint** page, enter `hello` for the filter and choose the **hello\-world** blueprint\.

1. On the **Configure triggers** page, choose **Next**\.

1. On the **Configure function** page, do the following:

   1. Enter a name and description for the Lambda function\. For example, name the function `LogAutoScalingEvent`\.

   1. Edit the sample code for the Lambda function\. For example:

      ```
      'use strict';
      
      exports.handler = (event, context, callback) => {
          console.log('LogAutoScalingEvent');
          console.log('Received event:', JSON.stringify(event, null, 2));
          callback(null, 'Finished');
      };
      ```

   1. For **Role**, choose **Choose an existing role**\. For **Existing role**, select your basic execution role\. Otherwise, create a basic execution role\.

   1. Choose **Next**\.

1. Choose **Create function**\.

## Step 2: Create a Rule<a name="as-create-rule"></a>

Create a rule to run your Lambda function whenever your Auto Scaling group launches or terminates an instance\.

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

   1. By default, the rule matches any Auto Scaling group in the Region\. To make the rule match a specific group, choose **Specific group name\(s\)** and select one or more groups\.

   1. By default, the rule matches any Auto Scaling group in the Region\. To make the rule match a specific Auto Scaling group, choose **Specific group name\(s\)** and select one or more Auto Scaling groups\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Target**, choose **Lambda function**\.

1. For **Function**, select the Lambda function that you created\.

1. Choose **Create**\.

## Step 3: Test the Rule<a name="as-test-rule"></a>

You can test your rule by manually scaling an Auto Scaling group so that it launches an instance\. After waiting a few minutes for the scale\-out event to occur, verify that your Lambda function was invoked\.

**To test your rule using an Auto Scaling group**

1. To increase the size of your Auto Scaling group, do the following:

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Auto Scaling**, **Auto Scaling Groups**\.

   1. Select the check box for your Auto Scaling group\.

   1. On the **Details** tab, choose **Edit**\. For **Desired**, increase the desired capacity by one\. For example, if the current value is 2, enter 3\. The desired capacity must be less than or equal to the maximum size of the group\. If your new value for **Desired** is greater than **Max**, you must update **Max**\. When you're finished, choose **Save**\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created, and choose **Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

   1. Select the name of the log stream to view the data provided by the function for the instance that you launched\.

1. \(Optional\) When you're finished, you can decrease the desired capacity by one so that the Auto Scaling group returns to its previous size\.