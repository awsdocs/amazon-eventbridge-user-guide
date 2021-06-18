# Tutorial: Log Amazon EC2 instance state changes<a name="eb-cloudwatch-logs-tutorial"></a>

In this tutorial, you create a [rule](eb-rules.md) causing state\-change notifications in [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) to be logged in [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)\. Tracking state change notifications can help you troubleshoot issues with you applications, or warn you about things that need your attention\.

**Topics**
+ [Step 1: Create a rule](#eb-cw-logs-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a rule<a name="eb-cw-logs-create-rule"></a>

Create a rule to log Amazon EC2 state\-change notifications in CloudWatch Logs

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

   1. For **Event type**, choose **EC2 Instance State\-change Notification**\.

   1. Choose **Any state** and **Any instance**\.

1. For **Target**, select **CloudWatch log group**\. 

1. For **Log group**, enter a name for the log group to receive the state\-change notifications\.

1. Choose **Create**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully created the rule\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.