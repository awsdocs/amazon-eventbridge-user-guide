# Creating an EventBridge Rule That Triggers on an Event from an AWS Resource<a name="create-eventbridge-rule"></a>

Use the following steps to create an EventBridge rule that triggers on an event emitted by an AWS service\.

When an AWS service in your account emits an event, it always goes to your account’s default event bus\. To write a rule that triggers on events from AWS services in your account, you must associate it with the default event bus\. You can create a rule on a custom event bus that looks for events from AWS services, but this rule will trigger only when you receive such an event from another account via cross\-account event delivery\. For more information, see [Sending and Receiving Events Between AWS Accounts](eventbridge-cross-account-event-delivery.md)\.

**To create a rule that triggers on an event**

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

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. Optionally, choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.