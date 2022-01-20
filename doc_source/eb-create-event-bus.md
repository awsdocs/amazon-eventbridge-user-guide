# Creating an Amazon EventBridge event bus<a name="eb-create-event-bus"></a>

You can create a custom [event bus](eb-event-bus.md) to receive [events](eb-events.md) from your applications\. Your applications can also send events to the default event bus\. When you create an event bus, you can attach a [resource\-based policy](eb-use-resource-based.md) to grant permissions to other accounts\. Then other accounts can send events to the event bus in the current account\.



**To create a custom event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. Choose **Create event bus**\.

1. Enter a name for the new event bus\.

1. Do one of the following:
   + Enter the policy that includes the permissions to grant for the event bus\. You can paste in a policy from another source or enter the JSON for the policy\. You can use one of the example policies and modify it for your environment\.
   + To use a template for the policy, choose **Load template**\. Modify the policy as appropriate for your environment, including adding additional actions that you authorize the principal in the policy to use\.

1. Choose **Create**\.