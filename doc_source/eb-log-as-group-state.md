# Tutorial: Log the state of an Auto Scaling group using EventBridge<a name="eb-log-as-group-state"></a>

You can run an [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function that logs an [events](eb-events.md) whenever an Auto Scaling group launches or terminates an Amazon EC2 instance that indicates whether an event was successful\.

For information about more scenarios that use Amazon EC2 Auto Scaling events, see [Getting CloudWatch Events When Your Auto Scaling Group Scales](https://docs.aws.amazon.com/autoscaling/latest/userguide/cloud-watch-events.html) in the *Amazon EC2 Auto Scaling User Guide*\.

In this tutorial, you create a Lambda function, and you create a [rule](eb-rules.md) in the EventBridge console that calls that function when an Amazon EC2 Auto Scaling group launches or terminates an instance\.

**Topics**
+ [Prerequisites](#eb-as-prereqs)
+ [Step 1: Create a Lambda function](#eb-as-create-lambda-function)
+ [Step 2: Create a rule](#eb-as-create-rule)
+ [Step 3: Test the rule](#eb-as-test-rule)
+ [Step 4: Confirm success](#success)
+ [Step 5: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-as-prereqs"></a>

To complete this tutorial, you'll need the following resources:
+ An Auto Scaling group\. For more information about creating one, see [Creating an Auto Scaling group using a launch configuration](https://docs.aws.amazon.com/autoscaling/latest/userguide/create-asg.html) in the Amazon EC2 Auto Scaling User Guide\.

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

1. Enter a name and description for the rule\. For example, name the rule `TestRule`

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select **default**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **AWS services**\.

1. For **Event pattern**, do the following:

   1. For **Event source**, select **Auto Scaling** from the drop\-down list\.

   1. For **Event type**, select **Instance Launch and Terminate** from the drop\-down list\.

   1. Choose **Any instance event** and **Any group name**\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **Lambda function** from the drop\-down list\.

1. For **Function**, select the Lambda function that you created in the **Step 1: Create a Lambda function** section\. In this example, select `LogAutoScalingEvent`\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 3: Test the rule<a name="eb-as-test-rule"></a>

You can test your rule by manually scaling an Auto Scaling group so that it launches an instance\. Wait a few minutes for the scale\-out event to occur, and then verify that your Lambda function was invoked\.

**To test your rule using an Auto Scaling group**

1. To increase the size of your Auto Scaling group, do the following:

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Auto Scaling**, **Auto Scaling Groups**\.

   1. Select the check box for your Auto Scaling group\.

   1. On the **Details** tab, choose **Edit**\. For **Desired**, increase the desired capacity by one\. For example, if the current value is **2**, enter **3**\. The desired capacity must be less than or equal to the maximum size of the group\. If your new value for **Desired** is greater than **Max**, you must update **Max**\. When you're finished, choose **Save**\.

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