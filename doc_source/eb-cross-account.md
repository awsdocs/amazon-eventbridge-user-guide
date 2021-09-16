# Sending and receiving Amazon EventBridge events between AWS accounts<a name="eb-cross-account"></a>

You can configure EventBridge to send and receive [events](eb-events.md) between AWS accounts\. When you configure EventBridge to send or receive events between accounts, you can specify which AWS accounts can send events to or receive events from the [event bus](eb-event-bus.md) in your account\. You can also allow or deny events from specific [rules](eb-rules.md) associated with the event bus, or events from specific sources\. For more information, see [Simplifying cross\-account access with Amazon EventBridge resource policies](http://aws.amazon.com/blogs/compute/simplifying-cross-account-access-with-amazon-eventbridge-resource-policies/)

**Note**  
If you use AWS Organizations, you can specify an organization and grant access to all accounts in that organization\. For more information, see [What is AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) in the *AWS Organizations User Guide*\.

**Note**  
If you're using an Incident Manager response plan as a target, all the response plans that are shared with your account are available by default\.

You can send and receive events between AWS accounts within the same Region in all Regions\.

The steps to configure EventBridge to send events to or receive events from a different account include the following:
+ On the *receiver* account, edit the permissions on an event bus to allow specified AWS accounts, an organization, or all AWS accounts to send events to the receiver account\.
+ On the *sender* account, set up one or more rules that have the receiver account's event bus as the target\.

  If the sender account inherits permissions to send events because from an AWS Organizations organization, the sender account also must have an IAM role with policies that enable it to send events to the receiver account\. If you use the AWS Management Console to create the rule that targets the event bus in the receiver account, the role is created automatically\. If you use the AWS CLI, you must create the role manually\.
+ On the *receiver* account, set up one or more rules that match events that come from the sender account\.

Events sent from one account to another are charged to the sending account as custom events\. The receiving account is not charged\. For more information, see [Amazon EventBridge Pricing](https://aws.amazon.com/eventbridge/pricing/)\.

If a receiver account sets up a rule that sends events received from a sender account on to a third account, these events are not sent to the third account\.

## Grant permissions to allow events from other AWS accounts<a name="eb-receiving-events-from-another-account"></a>

To receive events from other accounts or organizations, you must first edit the permissions on the event bus you intend to receive events on\. The default event bus accepts events from AWS services, other authorized AWS accounts, and `PutEvents` calls\. The permissions for an event bus are granted or denied using a resource\-based policy attached to the event bus\. In the policy, you can grant permissions to other AWS accounts using the account ID, or to an AWS Organizations organization the organization ID\. To learn more about event bus permissions, including example policies, see [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)\.

**Important**  
If you choose to receive events from all AWS accounts, be careful to create rules that match only the events to receive from others\. To create more secure rules, make sure that the event pattern for each rule contains an `Account` field with the account IDs of one or more accounts from which to receive events\. Rules that have an event pattern containing an Account field do not match events sent from accounts that are not listed in the `Account` field\. For more information, see [Amazon EventBridge events](eb-events.md)\.

## Rules for events between AWS accounts<a name="eb-writing-rules-that-match-events-from-another-account"></a>

If your account is set up to receive events from other AWS accounts, you can write rules that match those events\. Set the [event pattern](eb-event-patterns.md) of the rule to match the events you are receiving from the other account\.

Unless you specify `account` in the event pattern of a rule, any of your account's rules, both new and existing, that match events you receive from other accounts trigger based on those events\. If you are receiving events from another account, and you want a rule to trigger only on that event pattern when it is generated from your own account, you must add `account` and specify your own account ID to the event pattern of the rule\.

If you set up your AWS account to accept events from all AWS accounts, we strongly recommend that you add `account` to every EventBridge rule in your account\. This prevents rules in your account from triggering on events from unknown AWS accounts\. When you specify the `account` field in the rule, you can specify the account IDs of more than one AWS account in the field\.

To have a rule trigger on a matching event from any AWS account that you have granted permissions to, do not specify \* in the `account` field of the rule\. Doing so would not match any events, because \* never appears in the `account` field of an event\. Instead, just omit the `account` field from the rule\.

**To create a rule that sends events to a different AWS account using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, then on the **Rules** page choose **Create Rule**\.

1. Enter a name, and optionally a description for the rule\.

1. For **Event Source**, choose **Event Pattern**\.

1. Under **Event matching pattern**, do one of the following:
   + Choose **Pre\-defined pattern by service**, then select the **Service provider** and **Service name** to filter events on\. If you choose an AWS service also choose the **Event type**\. You may need to specify additional settings depending on the service and event type you choose\.
   + Choose **Custom pattern**, enter the **Event pattern** to use for the rule, then choose **Save**\.

1. For **Select event bus**, choose the event bus that you want to associate with this rule\.

1. Under **Select targets**, for **Target** choose **Event bus in a different account or Region**, then enter the ARN of the event bus in a different account to send events to\.

1. Do one of the following:
   + Choose **Create a new role for this specific resource** to have EventBridge create a new IAM role that has permissions to send events to the specified event bus\.
   + Choose **Use existing role** and then select the role to use\. If you choose to use an existing role, the role must have permissions to send events to the event bus in a different account\. To learn more, see [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)\.

1. Optionally, configure a dead\-letter queue for the rule\. To learn more, see [Event retry policy and using dead\-letter queues](eb-rule-dlq.md)\.

1. Choose **Create** to create the rule\.