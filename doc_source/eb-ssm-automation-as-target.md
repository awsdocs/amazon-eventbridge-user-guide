# Tutorial: Set AWS Systems Manager Automation as an EventBridge target<a name="eb-ssm-automation-as-target"></a>

You can use EventBridge to invoke [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) Automation on a regular timed schedule or when specific [events](eb-events.md) are detected\. This tutorial assumes that you're invoking Systems Manager Automation based on specific events\.

**Topics**
+ [Step 1: Create a Rule](#eb-ssm-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a Rule<a name="eb-ssm-create-rule"></a>

Create a rule to invoke Systems Manager Automation\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name** and **Event type**, choose the service and event type\. Depending on the service and event type that you choose, you might need to specify additional options\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Target**, choose **SSM Automation\.** 

1. For **Document**, choose the Systems Manager document to run\.

1. \(Optional\) To specify a version of the document, choose **Configure document version**\.

1. Under **Configure automation parameter\(s\)**, choose **No Parameter\(s\)** or **Constant**\. 

   If you choose **Constant**, specify the constants to pass to the document execution\.

1. Do one of the following: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**EventBridge creates the IAM role needed for your event to run\.
   + To use an IAM role that you created before, choose **Use existing role**

1. Choose **Create**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully created the rule\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.