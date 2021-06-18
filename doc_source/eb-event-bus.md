# Amazon EventBridge event buses<a name="eb-event-bus"></a>

An event bus is a pipeline that receives [events](eb-events.md)\. [Rules](eb-rules.md) associated with the event bus evaluate events as they arrive\. Each rule checks whether an event matches the rule's criteria\. You associate a rule with a specific event bus, so the rule only applies to events received by that event bus\. 

To manage permissions for an event bus, you can configure a [resource\-based policy](eb-use-resource-based.md) for it\. A *resource\-based policy* specifies which events to allow, and which entities have permission to create or modify rules or targets for an event\. For example, you can use a policy on an event bus to allow or deny events from sources such as a rule or an event bus in a different AWS account or AWS Region\. By using policies, you can aggregate all events from your application or organization in a single account and Region\.

You can configure up to 300 rules for each event bus\. If you have more than 300 rules in your environment, you can create custom event buses in your account and then associate an additional 300 rules with each event bus\. You can customize how events are received in your account by creating event buses with different permissions for different services\.

The most common event buses are:
+ The default event bus in each account receives events from AWS services\.
+ A custom event bus sends events to or receives events from a different account\.
+ A custom event bus sends events to or receives events from a different Region to aggregate events in a single location\.
+ A partner event bus receives events from a SaaS partner\. For more information, see [Receiving events from a SaaS partner with Amazon EventBridge](eb-saas.md)\.

**Topics**
+ [Permissions for Amazon EventBridge event buses](eb-event-bus-perms.md)
+ [Creating an Amazon EventBridge event bus](eb-create-event-bus.md)