# Amazon EventBridge quotas<a name="eb-quota"></a>

There are quotas for most aspects of EventBridge\.

**Topics**
+ [EventBridge quotas](#eb-limits)
+ [PutPartnerEvents quotas by Region](#eb-putpartnervents-limits)
+ [EventBridge Schema Registry quotas](#schema-quotas)
+ [EventBridge Pipes quotas](#eb-pipes-limits)

**Note**  
For a list of the quotas for EventBridge Scheduler, see [Quotas for EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/scheduler-quotas.html) in the *EventBridge Scheduler User Guide\.*

## EventBridge quotas<a name="eb-limits"></a>

EventBridge has the following quotas\.

The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)

In addition, EventBridge has the following quotas that are not managed through the Service Quotas console\.


| Name | Default | Description | 
| --- | --- | --- | 
|  Event buses  | Each supported Region: 100 |  Maximum event buses per account\.  | 
|  Event bus policy size  | Each supported Region: 10240 |  Maximum policy size, in characters\. This policy size increases each time you grant access to another account\. You can see your current policy and its size by using the DescribeEventBus API\.  | 
|  Schema discovery levels  | Each supported Region: 255 levels |  Maximum number of levels schema discovery will infer events that are nested\. Any events past 255 levels are ignored\.  | 

## PutPartnerEvents quotas by Region<a name="eb-putpartnervents-limits"></a>

If you have requirements for higher maximum limits, [contact support](https://console.aws.amazon.com/support/home?#/case/create?issueType=technical)\. 


| Regions | Transactions per second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  [PutPartnerEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutPartnerEvents.html) has a soft limit of 1,400 throughput requests per second and 3,600 burst requests per second by default in all Regions\.  | 

## EventBridge Schema Registry quotas<a name="schema-quotas"></a>

EventBridge Schema Registry has the following quotas\.

The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)

## EventBridge Pipes quotas<a name="eb-pipes-limits"></a>

EventBridge Pipes has the following quotas\. If you have requirements for higher maximum limits, [contact support](https://console.aws.amazon.com/support/home?#/case/create?issueType=technical)\.


| Resource | Regions | Default limit | 
| --- | --- | --- | 
|  Concurrent pipe executions per account  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  1000  | 
|  Concurrent pipe executions per account  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  3000  | 
|  Pipes per account  |  All  |  1000  | 