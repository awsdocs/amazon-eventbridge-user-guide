# Tutorial: Use EventBridge to relay events to AWS Systems Manager Run Command<a name="eb-ec2-run-command"></a>

You can use Amazon EventBridge to invoke [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) Run Command and perform actions on [Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) instances when specific [events](eb-events.md) happen\. In this tutorial, you set up Systems Manager Run Command to run shell commands and configure each new instance that is launched in an Amazon EC2 Auto Scaling group\.

**Note**  
This tutorial assumes that you assigned a tag to the Amazon EC2 Auto Scaling group, with `environment` as the key and `production` as the value\.

**Topics**
+ [Step 1: Create a Rule](#eb-run-cmd-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a Rule<a name="eb-run-cmd-create-rule"></a>

Create a rule to run shell commands and configure new Amazon EC2 instances\.

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

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Target**, select **SSM Run Command** \.

1. For **Document**, choose **AWS\-RunShellScript**\.

1. For **Target key**, type **tag:environment**\. For **Target value\(s\)**, enter **production** and choose **Add**\.

1. Under **Configure automation parameter\(s\)**, do the following:

   1. Choose **Constant**\.

   1. For **Commands**, enter a shell command and choose **Add**\. Repeat this step for all commands you want to run when an instance launches\. 

   1. If necessary, enter the appropriate information in **WorkingDirectory** and **ExecutionTimeout**\.

1. Do one of the following: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\. EventBridge creates the IAM role needed for your event to run\.
   + To use an IAM role that you created before, choose **Use existing role**

1. Choose **Create rule**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully created the rule\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.