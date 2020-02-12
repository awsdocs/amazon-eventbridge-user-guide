# Getting Started with Amazon EventBridge<a name="eventbridge-getting-set-up"></a>

Use the procedures in this section to create and delete EventBridge rules and event buses\. For tutorials on specific scenarios and specific targets, see [Amazon EventBridge Tutorials](eventbridge-tutorials.md)\.

**Topics**
+ [Creating an EventBridge Rule That Triggers on an Event from an AWS Resource](create-eventbridge-rule.md)
+ [Creating a Rule That Triggers on an AWS API Call via CloudTrail](create-eventbridge-cloudtrail-rule.md)
+ [Creating a Rule That Triggers on a Schedule](create-eventbridge-scheduled-rule.md)
+ [Receiving Events from an SaaS Partner](create-partner-event-bus.md)
+ [Creating an Event Bus](create-event-bus.md)
+ [Deleting or Disabling a Rule](delete-or-disable-rule.md)

**Limitations**
+ The targets you associate with a rule must be in the same Region as the rule\.
+ Some target types might not be available in every Region\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *Amazon Web Services General Reference*\.
+ Creating rules with built\-in targets is supported only in the AWS Management Console\.
+ If you create a rule with an encrypted Amazon SQS queue as a target, you must have the following section included in your AWS Key Management Service key policy\. It allows the event to be successfully delivered to the encrypted queue\.

  ```
  {
                  "Sid": "Allow CWE to use the key",
                  "Effect": "Allow",
                  "Principal": {
                                  "Service": "events.amazonaws.com"
                  },
                  "Action": [
                                  "kms:Decrypt",
                                  "kms:GenerateDataKey"
                  ],
                  "Resource": "*"
  }
  ```