# Creating a Rule That Triggers on an SaaS Partner Event<a name="create-rule-partner-events"></a>

Before you can create rules for events from SaaS partner applications and services, you must create a partner event bus and match it to that partner event source\. For more information, see [Receiving Events from an SaaS Partner](create-partner-event-bus.md)\.

**To create a rule that triggers on an event from an SaaS partner**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, choose **Event pattern**\.

1. Choose **Pre\-defined pattern by service**\.

1. For **Service provider**, choose **Service partners**\.

1. For **Service name**, choose the name of the partner\.

1. For **Event type**, choose **All Events** or choose the type of event to use for this rule\. If you choose **All Events**, all events emitted by this partner event source will match the rule\.

   If you want to customize the event pattern, choose **Edit**, make your changes, and choose **Save**\.

1. For **Service event bus**, confirm that the event bus that corresponds to this partner is selected\.

1. For **Select targets**, choose the AWS service that is to act when an event of the selected type is detected\.

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.