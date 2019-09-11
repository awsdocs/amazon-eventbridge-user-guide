# Tutorial: Log State Changes of Amazon EC2 Instances<a name="eventbridge-tutorial-cloudwatch-logs"></a>

In the example in this tutorial, you create a rule causing state\-change notifications in Amazon EC2 to be logged in Amazon CloudWatch Logs\.

**To create a rule to log Amazon EC2 state\-change notifications in CloudWatch Logs**

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