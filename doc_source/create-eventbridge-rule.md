# Creating a rule for an AWS service<a name="create-eventbridge-rule"></a>

When you create a rule for events, you specify a target action to take when EventBridge receives an event that matches the rule\. When an event matches the rule, EventBridge sends the event to the specified target and triggers the action defined in the rule\.

When an AWS service in your account emits an event, it always goes to the default event bus for your account\. To write a rule that triggers on events from AWS services in your account, you must associate it with the default event bus\. You can create a rule on a custom event bus that looks for events from AWS services, but this rule is triggered only when EventBridge receives such an event from another account via cross\-account event delivery\. For more information, see [Sending and Receiving Events Between AWS Accounts](eventbridge-cross-account-event-delivery.md)\.

Use the following steps to create an EventBridge rule that triggers on an event emitted by an AWS service\.

**To create a rule for an AWS service**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Choose **Pre\-defined pattern by service**\.

1. For **Service provider**, choose **AWS**\.

1. For **Service name**, choose the name of the service that emits the event\.

1. For **Event type**, choose **All Events** or choose the type of event to use for this rule\. If you choose **All Events**, all events emitted by this AWS service will match the rule\.

   If you want to customize the event pattern, choose **Edit**, make your changes, and choose **Save**\.

1. For **Select event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to trigger on matching events that come from your own AWS account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Select targets**, choose the AWS service that is to act when an event of the selected type is detected\.

1. The fields displayed vary by the service you choose\. Enter information specific to this target type as needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. For **Retry policy and dead\-letter queue:**, under **Retry policy**:

   1. For **Maximum age of event**, enter a value between 1 minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
   + Choose **None** to not use a dead\-letter queue\.
   + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
   + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. To learn more, see [Granting permissions to the DLQ](rule-dlq.md#dlq-perms)\.

1. Optionally, choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.