# Creating an Event Bus<a name="create-event-bus"></a>

Your account includes one default event bus, which receives events emitted by AWS services\. You can also configure your custom applications to send events to the default event bus\.

You can create two types of additional event buses in your account:
+ *Partner event buses*, which can receive events from applications and services created by AWS software as a service \(SaaS\) partners\. To receive events from SaaS partners, you need to create a partner event bus for each partner event source that you want to receive events from\.

  For more information, see [Receiving Events from an SaaS Partner](create-partner-event-bus.md)\.
+ *Custom event buses*, which can receive events from your custom applications and services\.

  Each event bus in your account can have up to 100 EventBridge rules associated with it, so if your account has many rules, you might want to create custom event buses to associate with some of the rules for your custom application events\. Another reason to create custom event buses is to apply different permissions to different event buses\. When you set permissions on an event bus, you can specify which other accounts or entire organizations can send events to the event bus\.

## Creating a Custom Event Bus<a name="create-custom-event-bus"></a>

You can create a custom event bus to receive events from your custom applications\. Your applications can also send events to your default event bus\.

**To create a custom event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. Choose **Create event bus**\.

1. Enter a name for the new event bus\.

1. To enable other accounts or entire organizations to send events to this event bus, choose **Other AWS account**, **Organization**, or both\.

   1. If you choose **Other AWS account**, choose either **Individual AWS account ID** or **All AWS accounts**\. If you choose **Individual AWS account ID**, enter the account ID\. To add more accounts, choose **Add account**\.

      If you choose **All AWS accounts**, be careful to create rules that match only the events that you want to receive from other accounts\. To create more secure rules, make sure that the event pattern for each rule contains an `Account` field with the account IDs of one or more accounts to receive events from\. Rules that have an event pattern containing an `Account` field don't match events sent from other accounts\.

   1. If you choose **Organization**, choose **My organization** to grant permissions to all accounts in the organization that your account is a member of\. Or choose **Other organization** and enter the organization ID including the `o-` prefix\. **My organization** is available only if your account is a member of an organization\.

      If you choose **Other organization** and want to add more organizations, choose **Add organization**\.

1. Choose **Create**\.