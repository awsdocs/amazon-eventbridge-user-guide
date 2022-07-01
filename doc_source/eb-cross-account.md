# Sending and receiving Amazon EventBridge events between AWS accounts<a name="eb-cross-account"></a>

You can configure EventBridge to send and receive [events](eb-events.md) between [event buses](eb-event-bus.md) in AWS accounts\. When you configure EventBridge to send or receive events between accounts, you can specify which AWS accounts can send events to or receive events from the event bus in your account\. You can also allow or deny events from specific [rules](eb-rules.md) associated with the event bus, or events from specific sources\. For more information, see [Simplifying cross\-account access with Amazon EventBridge resource policies](http://aws.amazon.com/blogs/compute/simplifying-cross-account-access-with-amazon-eventbridge-resource-policies/)

**Note**  
If you use AWS Organizations, you can specify an organization and grant access to all accounts in that organization\. In addition, the sending event bus must have IAM roles attached to them when sending events to another account\. For more information, see [What is AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) in the *AWS Organizations User Guide*\.

**Note**  
If you're using an Incident Manager response plan as a target, all the response plans that are shared with your account are available by default\.

You can send and receive events between event buses in AWS accounts within the same Region in all Regions and between accounts in different Regions as long as the destination Region is a supported [cross\-Region](eb-cross-region.md) destination Region\.

The steps to configure EventBridge to send events to or receive events from an event bus in a different account include the following:
+ On the *receiver* account, edit the permissions on an event bus to allow specified AWS accounts, an organization, or all AWS accounts to send events to the receiver account\.
+ On the *sender* account, set up one or more rules that have the receiver account's event bus as the target\.

  If the sender account inherits permissions to send events from an AWS Organization, the sender account also must have an IAM role with policies that enable it to send events to the receiver account\. If you use the AWS Management Console to create the rule that targets the event bus in the receiver account, the role is created automatically\. If you use the AWS CLI, you must create the role manually\.
+ On the *receiver* account, set up one or more rules that match events that come from the sender account\.

Events sent from one account to another are charged to the sending account as custom events\. The receiving account is not charged\. For more information, see [Amazon EventBridge Pricing](https://aws.amazon.com/eventbridge/pricing/)\.

If a receiver account sets up a rule that sends events received from a sender account on to a third account, these events are not sent to the third account\.



## Grant permissions to allow events from other AWS accounts<a name="eb-receiving-events-from-another-account"></a>

To receive events from other accounts or organizations, you must first edit the permissions on the event bus where you intend to receive events\. The default event bus accepts events from AWS services, other authorized AWS accounts, and `PutEvents` calls\. The permissions for an event bus are granted or denied using a resource\-based policy attached to the event bus\. In the policy, you can grant permissions to other AWS accounts using the account ID, or to an AWS organization using the organization ID\. To learn more about event bus permissions, including example policies, see [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)\.

**Important**  
If you choose to receive events from all AWS accounts, be careful to create rules that match only the events to receive from others\. To create more secure rules, make sure that the event pattern for each rule contains an `Account` field with the account IDs of one or more accounts from which to receive events\. Rules that have an event pattern containing an Account field do not match events sent from accounts that are not listed in the `Account` field\. For more information, see [Amazon EventBridge events](eb-events.md)\.

## Rules for events between AWS accounts<a name="eb-writing-rules-that-match-events-from-another-account"></a>

If your account is set up to receive events from event buses in other AWS accounts, you can write rules that match those events\. Set the [event pattern](eb-event-patterns.md) of the rule to match the events you are receiving from event buses in the other account\.

Unless you specify `account` in the event pattern of a rule, any of your account's rules, both new and existing, that match events you receive from event buses in other accounts trigger based on those events\. If you are receiving events from event buses in another account, and you want a rule to trigger only on that event pattern when it is generated from your own account, you must add `account` and specify your own account ID to the event pattern of the rule\.

If you set up your AWS account to accept events from event buses in all AWS accounts, we strongly recommend that you add `account` to every EventBridge rule in your account\. This prevents rules in your account from triggering on events from unknown AWS accounts\. When you specify the `account` field in the rule, you can specify the account IDs of more than one AWS account in the field\.

To have a rule trigger on a matching event from any event buses in AWS account that you have granted permissions to, do not specify \* in the `account` field of the rule\. Doing so would not match any events, because \* never appears in the `account` field of an event\. Instead, just omit the `account` field from the rule\.

**To create a rule that sends events to a different AWS account using the console**

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

1. For **Target types**, choose **EventBridge event bus** and choose **Event bus in another AWS account or Region**\.

1. Enter the ARN of the event bus to use as the target\.

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run\. Do one of the following:
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created earlier, choose **Use existing role** and select the existing role from the drop\-down list\.

1. \(Optional\) For **Additional settings**, do the following:

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
     + Choose **None** to not use a dead\-letter queue\.
     + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
     + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. For more information, see [Granting permissions to the dead\-letter queue](eb-rule-dlq.md#eb-dlq-perms)\.

1. \(Optional\) Choose **Add another target** to add another target for this rule\.

1. Choose **Next**\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.