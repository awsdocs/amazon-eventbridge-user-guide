# Amazon EventBridge User Guide

-----
*****Copyright &copy; 2020 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is Amazon EventBridge?](what-is-amazon-eventbridge.md)
+ [Setting Up Amazon EventBridge](getting-set-up-eventbridge.md)
+ [Getting Started with Amazon EventBridge](eventbridge-getting-set-up.md)
   + [Creating an EventBridge Rule That Triggers on an Event from an AWS Resource](create-eventbridge-rule.md)
   + [Creating an EventBridge Rule That Triggers on an AWS API Call Using AWS CloudTrail](create-eventbridge-cloudtrail-rule.md)
   + [Creating an EventBridge Rule That Triggers on a Schedule](create-eventbridge-scheduled-rule.md)
   + [Receiving Events from an SaaS Partner](create-partner-event-bus.md)
      + [Creating a Rule That Triggers on an SaaS Partner Event](create-rule-partner-events.md)
   + [Creating an Event Bus](create-event-bus.md)
   + [Deleting or Disabling an EventBridge Rule](delete-or-disable-rule.md)
+ [Amazon EventBridge Tutorials](eventbridge-tutorials.md)
   + [Tutorial: Use EventBridge to Relay Events to AWS Systems Manager Run Command](ec2-run-command.md)
   + [Tutorial: Log the State of an Amazon EC2 Instance Using EventBridge](log-ec2-instance-state.md)
   + [Tutorial: Log the State of an Auto Scaling Group Using EventBridge](log-as-group-state.md)
   + [Tutorial: Log Amazon S3 Object-Level Operations Using EventBridge](log-s3-data-events.md)
   + [Tutorial: Use Input Transformer to Customize What Is Passed to the Event Target](eventbridge-input-transformer-tutorial.md)
   + [Tutorial: Log AWS API Calls Using EventBridge](log-api-call.md)
   + [Tutorial: Schedule Automated Amazon EBS Snapshots Using EventBridge](take-scheduled-snapshot.md)
   + [Tutorial: Schedule AWS Lambda Functions Using EventBridge](run-lambda-schedule.md)
   + [Tutorial: Set AWS Systems Manager Automation as an EventBridge Target](ssm-automation-as-target.md)
   + [Tutorial: Relay Events to an Amazon Kinesis Stream Using EventBridge](relay-events-kinesis-stream.md)
   + [Tutorial: Run an Amazon ECS Task When a File Is Uploaded to an Amazon S3 Bucket](eventbridge-tutorial-ecs.md)
   + [Tutorial: Schedule Automated Builds Using AWS CodeBuild](eventbridge-tutorial-codebuild.md)
   + [Tutorial: Log State Changes of Amazon EC2 Instances](eventbridge-tutorial-cloudwatch-logs.md)
   + [Tutorial: Download Code Bindings for Events using the EventBridge Schema Registry](eventbridge-tutorial-schema-download-binding.md)
+ [Schedule Expressions for Rules](scheduled-events.md)
+ [Events and Event Patterns in EventBridge](eventbridge-and-event-patterns.md)
   + [AWS Events](aws-events.md)
   + [Event Patterns](filtering-examples-structure.md)
   + [Matching Null Values and Empty Strings in EventBridge Event Patterns](eventbridge-event-patterns-null-and-empty-strings.md)
   + [Arrays in EventBridge Event Patterns](arrays-in-eventbridge-event-patterns.md)
   + [Content-based Filtering with Event Patterns](content-filtering-with-event-patterns.md)
+ [Transforming Target Input](transform-input.md)
+ [Amazon EventBridge Schema Registry](eventbridge-schemas.md)
+ [EventBridge Event Examples from Supported AWS Services](event-types.md)
+ [Sending and Receiving Events Between AWS Accounts](eventbridge-cross-account-event-delivery.md)
+ [Adding Events with PutEvents](add-events-putevents.md)
   + [Calculating PutEvents Event Entry Sizes](calculate-putevents-entry-size.md)
+ [Using EventBridge with Interface VPC Endpoints](eventbridge-and-interface-VPC.md)
+ [Monitoring Usage with CloudWatch Metrics](eventbridge-monitoring-cloudwatch-metrics.md)
+ [Amazon EventBridge Managed Rules](eventbridge-managed-rules.md)
+ [Security in Amazon EventBridge](security-eventbridge.md)
   + [Data Protection in Amazon EventBridge](data-protection.md)
   + [Tag-based Policies](tag-based-policies.md)
   + [Identity and Access Management in Amazon EventBridge](auth-and-access-control-eventbridge.md)
      + [Overview of Managing Access Permissions to Your EventBridge Resources](iam-access-control-identity-based-eventbridge.md)
      + [Using Identity-Based Policies (IAM Policies) for EventBridge](iam-identity-based-access-control-eventbridge.md)
      + [Using Resource-Based Policies for EventBridge](resource-based-policies-eventbridge.md)
      + [EventBridge Permissions Reference](permissions-reference-eventbridge.md)
      + [Using IAM Policy Conditions for Fine-Grained Access Control](policy-keys-eventbridge.md)
   + [Logging and Monitoring in Amazon EventBridge](logging-cw-api-calls-eventbridge.md)
   + [Resilience in Amazon EventBridge](disaster-recovery-resiliency.md)
+ [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)
+ [Amazon EventBridge Quotas](cloudwatch-limits-eventbridge.md)
+ [Troubleshooting Amazon EventBridge](eventbridge-troubleshooting.md)
+ [Document History](document-history-eventbridge.md)
+ [AWS Glossary](glossary.md)