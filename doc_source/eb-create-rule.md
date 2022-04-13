# Creating Amazon EventBridge rules that react to events<a name="eb-create-rule"></a>

To create a [rule](eb-rules.md) for [events](eb-events.md), you specify an action to take when EventBridge receives an event that matches the [event pattern](eb-event-patterns.md) in the rule\. When an event matches, EventBridge sends the event to the specified [target](eb-targets.md) and triggers the action defined in the rule\.



**To create a rule that reacts to events**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **AWS services**\.

1. \(Optional\) For **Sample events**, choose the type of event\.

1. For **Event pattern**, do one the following:
   + To use a template to create your event pattern, choose **Event pattern form** and choose the **Event source** and **Event type**\. If you choose **All Events** as the event type, all events emitted by this AWS service will match the rule\.

     To customize the template, choose **Custom pattern \(JSON editor\)** and make your changes\.
   + To use a custom event pattern, choose **Custom pattern \(JSON editor\)** and create your event pattern\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose the AWS service that you want to send information to when EventBridge detects an event that matches the event pattern\.

1. The fields displayed vary depending on the service you choose\. Enter information specific to this target type as needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run\. Do one of the following:
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created earlier, choose **Use existing role** and select the existing role from the drop\-down list\.

1. \(Optional\) For **Additional settings**, do the following:

   1. For **Maximum age of event**, enter a value between one minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
      + Choose **None** to not use a dead\-letter queue\.
      + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
      + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. For more information, see [Granting permissions to the dead\-letter queue](eb-rule-dlq.md#eb-dlq-perms)\.

1. \(Optional\) Choose **Add another target** to add another target for this rule\.

1. Choose **Next**\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.