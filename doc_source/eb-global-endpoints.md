# Making applications Regional\-fault tolerant with global endpoints and event replication<a name="eb-global-endpoints"></a>

You can improve your application's availability with Amazon EventBridge global endpoints\. Global endpoints help make your application regional\-fault tolerant at no additional cost\. To start, you assign an Amazon Route 53 health check to the endpoint\. When failover is initiated, the health check reports an “unhealthy” state\. Within minutes of failover initiation, all custom [events](eb-events.md) are routed to an [event bus](eb-event-bus.md) in the secondary Region and are processed by that event bus\. Once the health check reports a “healthy” state, events are processed by the event bus in the primary Region\.

When you use global endpoints, you can enable [event replication](#eb-ge-event-replication)\. Event replication sends all custom events to the event buses in the primary and secondary Regions using managed rules\.

**Note**  
If you're using custom buses, you'll need a custom bus in each Region with the same name and in the same account for failover to work properly\.

**Topics**
+ [Recovery Time & Recovery Point Objectives](#eb-ge-rpo-rto)
+ [Event replication](#eb-ge-event-replication)
+ [Create a global endpoint](#eb-ge-create-endpoint)
+ [Working with global endpoints by using an AWS SDK](#eb-ge-sdk-update)
+ [Available Regions](#eb-ge-avail-regions)
+ [Best practices for working with Amazon EventBridge global endpoints](eb-ge-best-practices.md)
+ [AWS CloudFormation template for setting up the Route 53 health check](eb-ge-cfn.md)

## Recovery Time & Recovery Point Objectives<a name="eb-ge-rpo-rto"></a>

The Recovery Time Objective \(RTO\) is the time that it takes for the secondary Region to start receiving events after a failure\. For RTO, the time includes time period for triggering CloudWatch alarms and updating statuses for Route 53 health checks\. The Recovery Point Objective \(RPO\) is the measure of the data that will be left unprocessed during a failure\. For RPO, the time includes events that are not replicated to the secondary Region and are stuck in the primary Region until the service or Region recovers\. With global endpoints, if you follow our prescriptive guidance for alarm configuration, you can expect the RTO and RPO to be 360 seconds with a maximum of 420 seconds\.

## Event replication<a name="eb-ge-event-replication"></a>

Events are processed in the secondary Region asynchronously\. This means that events are not guaranteed to be processed at the same time in both Regions\. When failover is triggered, the events are processed by the secondary Region and will be processed by the primary Region when it’s available\. Enabling event replication will increase your monthly costs\. For more information, see [Amazon EventBridge pricing](http://aws.amazon.com/eventbridge/pricing)

We recommend enabling event replication when setting up global endpoints for the following reasons:
+ Event replication helps you verify that your global endpoints are configured correctly\. This helps to ensure that you’ll be covered in the event of failover\.
+ Event replication is required to automatically recover from a failover event\. If you don’t have event replication enabled, you’ll have to manually reset the Route 53 health check to “healthy” before events will go back to the primary Region\.

### Replicated event payload<a name="eb-ge-event-replication-ep"></a>

The following is an example of a replicated event payload:

**Note**  
For `region`, the Region that the event was replicated from is listed\.

```
{
    "version": "0",
    "id": "a908baa3-65e5-ab77-367e-527c0e71bbc2",
    "detail-type": "Test",
    "source": "test.service.com",
    "account": "0123456789",
    "time": "1900-01-01T00:00:00Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:events:us-east-1:0123456789:endpoint/MyEndpoint"
    ],
    "detail": {
        "a": "b"
    }
}
```

## Create a global endpoint<a name="eb-ge-create-endpoint"></a>

Complete the following steps to set up a global endpoint:

1. Make sure that you have matching event buses and rules in both the primary and secondary Region\.

1. Create a [Route 53 health check](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/health-checks-creating.html) to monitor your event buses\. For assistance in creating your health check, choose **New Health Check** when creating your global endpoint\.

1. Create your global endpoint\.

Once you have set up the Route 53 health check, you can create a global endpoint\.

### To create a global endpoint by using the console<a name="eb-ge-create-endpoint-console"></a>

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Global endpoints**\.

1. Choose **Create Endpoint**\.

1. Enter a name and description for the endpoint\.

1. For **Event bus in primary Region**, choose the event bus you’d like the endpoint associated with\.

1. For **Secondary Region**, choose the Region you'd like to direct events to in the event of a failover\.
**Note**  
The **Event bus in secondary Region** is auto\-filled and not editable\.

1. For **Route 53 health check for triggering failover and recovery**, choose the health check that the endpoint will monitor\. If you don't already have a health check, choose **New Health check** to open the AWS CloudFormation console and create a health check using a CloudFormation template\.
**Note**  
Missing data will cause the health check to fail\. If you only need to send events intermittently, consider using a longer **MinimumEvaluationPeriod**, or treat missing data as 'missing' instead of 'breaching'\.

1. \(Optional\) For **Event replication** do the following:

   1. Select **Event replication enabled**\.

   1. For **Execution role**, choose whether to create a new AWS Identity and Access Management role or use an existing one\. Do the following:
      + Choose **Create a new role for this specific resource**\. Optionally, you can update the **Role name** to create a new role\.
      + Choose **Use existing role**\. Then, for **Execution role**, choose the desired role to use\.

1. Choose **Create**\.

### To create a global endpoint by using the API<a name="eb-ge-create-endpoint-api"></a>

To create a global endpoint using the EventBridge API, see [CreateEndpoint](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_CreateEndpoint.html) in the Amazon EventBridge API Reference\.

### To create a global endpoint by using AWS CloudFormation<a name="eb-ge-create-endpoint-cfn"></a>

To create a global endpoint using the AWS CloudFormation API, see [AWS::Events::Endpoints](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-endpoint.html) in the AWS CloudFormation User Guide\.

## Working with global endpoints by using an AWS SDK<a name="eb-ge-sdk-update"></a>

**Note**  
Support for C\+\+ is coming soon\.

When using an AWS SDK to work with global endpoints, keep the following in mind:
+ You'll need to have the AWS Common Runtime \(CRT\) library installed for your specific SDK\. If you don't have the CRT installed, you'll get an exception message indicating what needs to be installed\. For more information, see the following:
  + [AWS Common Runtime \(CRT\) libraries](https://docs.aws.amazon.com/sdkref/latest/guide/common-runtime.html)
  + [awslabs/aws\-crt\-java](https://github.com/awslabs/aws-crt-java)
  + [awslabs/aws\-crt\-nodejs](https://github.com/awslabs/aws-crt-nodejs)
  + [awslabs/aws\-crt\-python](https://github.com/awslabs/aws-crt-python)
+ Once you have created a global endpoint, you'll need to add the `endpointId` and `EventBusName` to any `PutEvents` calls that you use\.
+ Global endpoints support Signature Version 4A\. This version of SigV4 allows requests to be signed for multiple AWS Regions\. This is useful in API operations that might result in data access from one of several Regions\. When using the AWS SDK, you supply your credentials and the requests to global endpoints will use Signature Version 4A without additional configuration\. For more information about SigV4A, see [Signing AWS API requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html ) in the *AWS General Reference*\.

## Available Regions<a name="eb-ge-avail-regions"></a>

The following Regions support global endpoints:
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Milan\)
+ Europe \(Paris\)
+ Europe \(Stockholm\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Osaka\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ South America \(São Paulo\)