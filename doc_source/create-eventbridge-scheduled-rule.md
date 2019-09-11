# Creating an EventBridge Rule That Triggers on a Schedule<a name="create-eventbridge-scheduled-rule"></a>

Use the following steps to create an EventBridge rule that triggers on a regular schedule\. You can create scheduled rules only using the default event bus\.

**To create a rule that triggers on a regular schedule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Schedule**\.

1. Either choose **Fixed rate of** and specify how often the task is to run, or choose **Cron expression** and specify a cron expression that defines when the task is to be triggered\. For more information about cron expression syntax, see [Schedule Expressions for Rules](scheduled-events.md)\.

1. For **Select event bus**, choose **AWS default event bus**\. Scheduled rules are supported only on the default event bus\.

1. For **Select targets**, choose the AWS service that is to act on the specified schedule\.

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.