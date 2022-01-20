# Amazon EventBridge rules<a name="eb-rules"></a>

A rule matches incoming [events](eb-events.md) and sends them to [targets](eb-targets.md) for processing\. A single rule can send an event to multiple targets, which then run in parallel\. Rules are based either on an [event pattern](eb-event-patterns.md) or a schedule\. An *event pattern* defines the event structure and the fields that a rule matches\. Rules that are based on a schedule perform an action at regular intervals\.

AWS services can create and manage EventBridge rules in your AWS account that are needed for certain functions in those services\. These are called *managed rules*\. 

When a service creates a managed rule, it can also create an [IAM policy](eb-iam.md) that grants permission to that service to create the rule\. IAM policies created this way are scoped narrowly with resource\-level permissions to allow the creation of only the necessary rules\.

You can delete managed rules by using the **Force delete** option, but you should only delete them if you're sure that the other service no longer needs the rule\. Otherwise, deleting a managed rule causes the features that rely on it to stop working\.



**Topics**
+ [Creating Amazon EventBridge rules that react to events](eb-create-rule.md)
+ [Creating an Amazon EventBridge rule that runs on a schedule](eb-create-rule-schedule.md)
+ [Disabling or deleting an Amazon EventBridge rule](eb-delete-rule.md)
+ [Event retry policy and using dead\-letter queues](eb-rule-dlq.md)
+ [Using Amazon EventBridge and AWS Serverless Application Model templates](eb-use-sam.md)