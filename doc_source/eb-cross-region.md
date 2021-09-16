# Sending and receiving Amazon EventBridge events between AWS Regions<a name="eb-cross-region"></a>

You can configure EventBridge to send and receive [events](eb-events.md) between AWS Regions\. You can also allow or deny events from specific Regions, specific [rules](eb-rules.md) associated with the event bus, or events from specific sources\. For more information, see [Introducing cross\-Region event routing with Amazon EventBridge](http://aws.amazon.com/blogs/compute/introducing-cross-region-event-routing-with-amazon-eventbridge/)

The current list of supported destination Regions is:
+ US East \(N\. Virginia\)
+ US West \(Oregon\)
+ Europe \(Ireland\)

**To create a rule that sends events to a different AWS Region using the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, then on the **Rules** page choose **Create Rule**\.

1. Enter a name, and optionally a description for the rule\.

1. For **Event Source**, choose **Event Pattern**\.

1. Under **Event matching pattern**, do one of the following:
   + Choose **Pre\-defined pattern by service**, then select the **Service provider** and **Service name** to filter events on\. If you choose an AWS service also choose the **Event type**\. You may need to specify additional settings depending on the service and event type you choose\.
   + Choose **Custom pattern**, enter the **Event pattern** to use for the rule, then choose **Save**\.

1. For **Select event bus**, choose the event bus that you want to associate with this rule\.

1. Under **Select targets**, for **Target** choose **Event bus in a different account or Region**, then enter the ARN of the event bus in a different Region to send events to\.

1. Do one of the following:
   + Choose **Create a new role for this specific resource** to have EventBridge create a new IAM role that has permissions to send events to the specified event bus\.
   + Choose **Use existing role** and then select the role to use\. If you choose to use an existing role, the role must have permissions to send events to the event bus in a different Region\. To learn more, see [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)\.

1. Optionally, configure a dead\-letter queue for the rule\. To learn more, see [Event retry policy and using dead\-letter queues](eb-rule-dlq.md)\.

1. Choose **Create** to create the rule\.