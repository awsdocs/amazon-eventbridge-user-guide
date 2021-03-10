# Configure EventBridge to receive events from a SaaS integration<a name="eb-saas-receive-events"></a>

The following procedure describes how to configure EventBridge to receive events from a SaaS integration partner\.

**To receive events from an SaaS partner**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Partner event sources**\.

1. Find the partner that you want and choose **Set up** for that partner\.

1. Choose **Copy** to copy your account ID to the clipboard\.

1. In the navigation pane, choose **Partner event sources**\.

1. Go to the partner's website and follow the instructions to create a partner event source\. Use your account ID for this\. The event source that you create will be available to only your account\.

1. Go back to the EventBridge console and choose **Partner event sources** in the navigation pane\.

1. Select the button next to the partner event source, and choose **Associate with event bus**\. 

   The status of that event source changes from `Pending` to `Active`, and the name of the event bus is updated to match the partner event source name\. You can now start creating rules that trigger on events from that partner event source\. For more information, see [Creating a rule that triggers on SaaS partner events](#create-rule-partner-events)\.

## Creating a rule that triggers on SaaS partner events<a name="create-rule-partner-events"></a>

Before you can create rules for events from SaaS partner applications and services, you must create a partner event bus and match it to that partner event source\. For more information, see [Receiving events from a SaaS partner](create-partner-event-bus.md)\.

**To create a rule that triggers on an event from a SaaS partner**

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