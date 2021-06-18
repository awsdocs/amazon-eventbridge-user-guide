# Log and monitor in Amazon EventBridge<a name="eb-logging-monitoring"></a>

Amazon EventBridge works with AWS CloudTrail, a service that records actions from AWS services\. CloudTrail captures API calls made by or on behalf of your AWS account from the EventBridge console and to EventBridge API operations\. 

 Using the information collected by CloudTrail, you can determine what request was made to EventBridge, the IP address from which the request was made, who made the request, when it was made, and more\. 

For more information about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Topics**
+ [EventBridge information in CloudTrail](#eb-info-in-cloudtrail)
+ [Example: EventBridge log file entries](#eb-understanding-entries-in-cloudtrail)

## EventBridge information in CloudTrail<a name="eb-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create your account\. When an event occurs in EventBridge, CloudTrail records the event in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For a record of events in your AWS account, including events for EventBridge, create a trail\. A *trail* is a configuration that CloudTrail uses to deliver log files to an Amazon S3 bucket\. By default, the trail logs events from all Regions in the AWS partition and then delivers the log files to an S3 bucket\. You can configure other AWS services to analyze and act on the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

You can log the following EventBridge actions as events in CloudTrail log files:
+ [DeleteRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DeleteRule.html)
+ [DescribeEventBus](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)
+ [DescribeRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DescribeRule.html)
+ [DisableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_DisableRule.html)
+ [EnableRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_EnableRule.html)
+ [ListRuleNamesByTarget](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)
+ [ListRules](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)
+ [ListTargetsByRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)
+ [PutPermission](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutPermission.html)
+ [PutRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutRule.html)
+ [PutTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)
+ [RemoveTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)
+ [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html)

Every event and log entry contains information about who generated the entry\. You can use this information to determine the following: 
+ Whether the request was made with root or AWS Identity and Access Management \(IAM\) user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Example: EventBridge log file entries<a name="eb-understanding-entries-in-cloudtrail"></a>

A *trail* is a configuration that CloudTrail uses to deliver events as log files to an Amazon S3 bucket\. CloudTrail log files contain log entries\. An event represents a log entry, and it includes information about the requested action, the date and time of the action, and request parameters\.

**Note**  
CloudTrail log files don't appear in any specific order\.

The following CloudTrail log file entry shows that a user called the EventBridge **PutRule** action\.

```
{
         "eventVersion":"1.03",
         "userIdentity":{
            "type":"Root",
            "principalId":"123456789012",
            "arn":"arn:aws:iam::123456789012:root",
            "accountId":"123456789012",
            "accessKeyId":"AKIAIOSFODNN7EXAMPLE",
            "sessionContext":{
               "attributes":{
                  "mfaAuthenticated":"false",
                  "creationDate":"2015-11-17T23:56:15Z"
               }
            }
         },
         "eventTime":"2015-11-18T00:11:28Z",
         "eventSource":"events.amazonaws.com",
         "eventName":"PutRule",
         "awsRegion":"us-east-1",
         "sourceIPAddress":"AWS Internal",
         "userAgent":"AWS CloudWatch Console",
         "requestParameters":{
            "description":"",
            "name":"cttest2",
            "state":"ENABLED",
            "eventPattern":"{\"source\":[\"aws.ec2\"],\"detail-type\":[\"EC2 Instance State-change Notification\"]}",
            "scheduleExpression":""
         },
         "responseElements":{
            "ruleArn":"arn:aws:events:us-east-1:123456789012:rule/cttest2"
         },
         "requestID":"e9caf887-8d88-11e5-a331-3332aa445952",
         "eventID":"49d14f36-6450-44a5-a501-b0fdcdfaeb98",
         "eventType":"AwsApiCall",
         "apiVersion":"2015-10-07",
         "recipientAccountId":"123456789012"
}
```