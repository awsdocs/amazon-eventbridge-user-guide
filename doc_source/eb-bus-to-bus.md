# Sending and receiving Amazon EventBridge events between event buses in the same account and Region<a name="eb-bus-to-bus"></a>

You can configure EventBridge to send and receive [events](eb-events.md) between [event buses](eb-event-bus.md) in the same AWS account and Region\. 

When you configure EventBridge to send or receive events between event buses, you use IAM roles on the *sender* event bus to give the *sender* event bus permission to send events to the *receiver* event bus\. You use [Resource\-based](eb-use-resource-based.md) policies on the *receiver* event bus to give the *receiver* event bus permission to receive events from the *sender* event bus\. You can also allow or deny events from certain event buses, specific [rules](eb-rules.md) associated with the event bus, or events from specific sources\. For more information about event bus permissions, including example policies, see [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)\.

The steps to configure EventBridge to send events to or receive events between event buses in your account include the following:
+ To use an existing IAM role, you need to give either the sender event bus permissions to the receiver event bus or the receiver event bus permissions to the sender event bus\.
+ On the *sender* event bus, set up one or more rules that have the receiver event bus as the target and create and IAM role\.
+ On the *receiver* event bus, edit the permissions to allow events to be passed from the other event bus\.
+ On the *receiver* event, set up one or more rules that match events that come from the sender event bus\.
**Note**  
EventBridge can't route events received from a sender event bus to a third event bus\.

Events sent from one event bus to another are charged as custom events\. For more information, see [Amazon EventBridge Pricing](https://aws.amazon.com/eventbridge/pricing/)\.

To send events to another event bus, you create a rule with an event bus as a target\.

**To create a rule that sends events to a different event bus using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, then on the **Rules** page, choose **Create Rule**\.

1. Enter a name, and optionally a description for the rule\.

1. For **Event Source**, choose **Event Pattern**\.

1. Under **Event matching pattern**, do one of the following:
   + Choose **Pre\-defined pattern by service**, and then select the **Service provider** and **Service name** to filter events on\. If you choose an AWS service, also choose the **Event type**\. You might need to specify additional settings depending on the service and event type you choose\.
   + Choose **Custom pattern**, enter the **Event pattern** to use for the rule, and then choose **Save**\.

1. For **Select event bus**, choose the event bus to associate with this rule\.

1. Under **Select targets**, for **Target**, choose **Event bus in the same account and Region**, and then from the list of available event buses, select a different event bus than the one you selected in step 6\.

1. Choose **Create a new role for this specific resource** to have EventBridge create a new IAM role that has permissions to send events to the specified event bus\. 

1. \(Optional\) Configure a dead\-letter queue for the rule\. For more information, see [Event retry policy and using dead\-letter queues](eb-rule-dlq.md)\.

1. Choose **Create** to create the rule\.