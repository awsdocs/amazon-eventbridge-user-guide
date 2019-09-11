# Tutorial: Use EventBridge to Relay Events to AWS Systems Manager Run Command<a name="ec2-run-command"></a>

You can use Amazon EventBridge to invoke AWS Systems Manager Run Command and perform actions on Amazon EC2 instances when certain events happen\. In this tutorial, set up Systems Manager Run Command to run shell commands and configure each new instance that is launched in an Amazon EC2 Auto Scaling group\. This tutorial assumes that you have already assigned a tag to the Amazon EC2 Auto Scaling group, with `environment` as the key and `production` as the value\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **Auto Scaling**\.

   1. For **Event type**, choose **Instance Launch and Terminate**\.

   1. Choose **Specific instance event\(s\)**, **EC2 Instance\-launch Lifecycle Action**\.

   1. By default, the rule matches any Amazon EC2 Auto Scaling group in the Region\. To make the rule match a specific group, choose **Specific group name\(s\)** and select one or more groups\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Target**, select **SSM Run Command** \.

1. For **Document**, choose **AWS\-RunShellScript**\.

   For **Target key**, type **tag:environment**\. For **Target value\(s\)**, enter **production** and choose **Add**\.

1. Under **Configure automation parameter\(s\)**, do the following:

   1. Choose **Constant**\.

   1. For **Commands**, enter a shell command and choose **Add**\. Repeat this step for all commands to run when an instance launches\. 

   1. If necessary, enter the appropriate information in **WorkingDirectory** and **ExecutionTimeout**\.

1. EventBridge can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. Choose **Create rule**\.