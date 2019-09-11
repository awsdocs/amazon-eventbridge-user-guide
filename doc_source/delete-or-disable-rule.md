# Deleting or Disabling an EventBridge Rule<a name="delete-or-disable-rule"></a>

Use the following steps to delete or disable an EventBridge rule\.

**To delete or disable a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

   Under **Event bus**, select the event bus that is associated with the rule\.

1. Do one of the following:

   1. To delete a rule, select the button next to the rule and choose **Actions**, **Delete**, **Delete**\.

      If the rule is a managed rule, you must enter the name of the rule to acknowledge that it is a managed rule and that deleting it may stop functionality in the service that created the rule\. To continue, enter the rule name and choose **Force delete**\. For more information, see [Amazon EventBridge Managed Rules](eventbridge-managed-rules.md)\.

   1. To temporarily disable a rule, select the button next to the rule and choose **Disable**, **Disable**\.

      You can't disable a managed rule\.