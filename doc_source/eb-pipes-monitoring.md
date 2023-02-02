# Logging and monitoring Amazon EventBridge Pipes using AWS CloudTrail and Amazon CloudWatch Logs<a name="eb-pipes-monitoring"></a>

You can log EventBridge Pipes invocations and using CloudTrail and monitor the health of your pipes using CloudWatch metrics\.

## CloudWatch metrics<a name="pipes-monitoring-cloudwatch"></a>

EventBridge Pipes sends metrics to Amazon CloudWatch every minute for everything from a pipe executions being throttled to a target successfully being invoked\.


| Metric | Description | 
| --- | --- | 
|  `ConcurrentExecutions`  |  The number of concurrent executions of a pipe\. Valid Dimensions: PipeName, AwsAccountId Units: Count  | 
|  `EventCount`  |  The number of events a pipe has processed\. Valid Dimensions: PipeName Units: Count  | 
|  `EventSize`  |  The size of the payload of the event that invoked the pipe\. Valid Dimensions: PipeName Units: Bytes  | 
|  `ExecutionThrottled`  |  How many executions of a pipe were throttled\.  This value will be `0` if no executions were throttled\.  Valid Dimensions: PipeName Units: Count  | 
|  `ExecutionTimeout`  |  How many executions of a pipe timed out before completing execution\.  This value will be `0` if no executions timed out\.  Valid Dimensions: PipeName Units: Count  | 
|  `ExecutionFailed`  |  How many executions of a pipe failed\.  This value will be `0` if no executions failed\.  Valid Dimensions: PipeName Units: Count  | 
|  `ExecutionPartiallyFailed`  |  How many executions of a pipe partially failed\.  This value will be `0` if no executions partially failed\.  Valid Dimensions: PipeName Units: Count  | 
|  `EnrichmentStageDuration`  |  How long the enrichment stage took to complete\. Valid Dimensions: PipeName Units: Milliseconds  | 
|  `EnrichmentStageFailed`  |  How many executions of a pipe's enrichment stage failed\.  This value will be `0` if no executions failed\.  Valid Dimensions: PipeName Units: Count  | 
|  `TargetStageDuration`  |  How long the target stage took to complete\. Valid Dimensions: PipeName Units: Milliseconds  | 
|  `TargetStageFailed`  |  How many executions of a pipe's target stage failed\.  This value will be `0` if no executions failed\.  Valid Dimensions: PipeName Units: Count  | 

## Dimensions for CloudWatch metrics<a name="pipes-monitoring-cloudwatch-dimensions"></a>

CloudWatch metrics have *dimensions*, or sortable attributes, which are listed below\.


|  Dimension  |  Description  | 
| --- | --- | 
|  AwsAccountId  |  Filters the available metrics by account ID\.  | 
|  PipeName  |  Filters the available metrics by pipe name\.  | 

## CloudTrail logging<a name="pipes-monitoring-cloudtrail"></a>

EventBridge Pipes works with AWS CloudTrail, a service that records actions from AWS services\. CloudTrail captures API calls made by or on behalf of your AWS account from the EventBridge console and to EventBridge Pipes API operations\. 

 Using the information collected by CloudTrail, you can determine what request was made to EventBridge, the IP address from which the request was made, who made the request, when it was made, and more\. 

For more information about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

CloudTrail is enabled on your AWS account when you create your account\. When an event occurs in EventBridge, CloudTrail records the event in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For a record of events in your AWS account, including events for EventBridge, create a trail\. A *trail* is a configuration that CloudTrail uses to deliver log files to an Amazon S3 bucket\. By default, the trail logs events from all Regions in the AWS partition and then delivers the log files to an S3 bucket\. You can configure other AWS services to analyze and act on the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

You can log the following EventBridge Pipes actions as events in CloudTrail log files:
+ [CreatePipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_CreatePipe.html)
+ [DeletePipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_DeletePipe.html)
+ [DescribePipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_DescribePipe.html)
+ [ListPipes](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_ListPipes.html)
+ [StartPipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_StartPipe.html)
+ [StopPipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_StopPipe.html)
+ [UpdatePipe](https://docs.aws.amazon.com/eventbridge/latest/pipes-reference/API_UpdatePipe.html)

### CloudTrail log entries for actions taken by EventBridge Pipes<a name="monitoring-cloudtrail-samples"></a>

EventBridge Pipes assumes the provided IAM role when reading events from sources, invoking enrichments, or invoking targets\. For CloudTrail entries related to actions taken in your account on all enrichments, targets, and Amazon SQS, Kinesis, and DynamoDB sources, the `sourceIPAddress` and `invokedBy` fields will include `pipes.amazonaws.com`\.

**Sample CloudTrail log entry for all enrichments, targets, and Amazon SQS, Kinesis, and DynamoDB sources**

```
{
  "eventVersion": "1.08",
  "userIdentity": {
    "type": "AssumedRole",
    "principalId": "...",
    "arn": "arn:aws:sts::111222333444:assumed-role/...",
    "accountId": "111222333444",
    "accessKeyId": "...",
    "sessionContext": {
      "sessionIssuer": {
        "type": "Role",
        "principalId": "...",
        "arn": "...",
        "accountId": "111222333444",
        "userName": "userName"
      },
      "webIdFederationData": {},
      "attributes": {
        "creationDate": "2022-09-22T21:41:15Z",
        "mfaAuthenticated": "false"
      }
    },
    "invokedBy": "pipes.amazonaws.com"
  },
  "eventTime": ",,,",
  "eventName": "...",
  "awsRegion": "us-west-2",
  "sourceIPAddress": "pipes.amazonaws.com",
  "userAgent": "pipes.amazonaws.com",
  "requestParameters": {
    ...
  },
  "responseElements": null,
  "requestID": "...",
  "eventID": "...",
  "readOnly": true,
  "eventType": "AwsApiCall",
  "managementEvent": true,
  "recipientAccountId": "...",
  "eventCategory": "Management"
}
```

For all other sources, the `sourceIPAddress` field of the CloudTrail log entries will have a dynamic IP address and shouldn't be relied upon for any integration or event categorization\. In addition, these entries won't have the `invokedBy` field\.

**Sample CloudTrail log entry for all other sources**

```
{
  "eventVersion": "1.08",
  "userIdentity": {
    ...
  },
  "eventTime": ",,,",
  "eventName": "...",
  "awsRegion": "us-west-2",
  "sourceIPAddress": "127.0.0.1",
  "userAgent": "Python-httplib2/0.8 (gzip)",
}
```