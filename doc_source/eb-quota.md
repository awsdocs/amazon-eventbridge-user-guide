# Amazon EventBridge quotas<a name="eb-quota"></a>

There are quotas for most aspects of EventBridge\.

**Topics**
+ [EventBridge quotas](#eb-limits)
+ [PutEvents quotas by Region](#eb-putevents-limits)
+ [PutPartnerEvents quotas by Region](#eb-putpartnervents-limits)
+ [Invocation quotas by Region](#eb-invocations-limits)

## EventBridge quotas<a name="eb-limits"></a>

EventBridge has the following quotas\.


| Resource | Default limit | 
| --- | --- | 
|  Event publishing API requests  |  [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) operations are limited based on AWS Region\. See [PutEvents quotas by Region](#eb-putevents-limits)\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  All other API requests  |  All EventBridge APIs other than `PutEvents` are limited to 50 requests per second by default\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  API destinations  |  Default: 3,000 The maximum number of [API destinations](eb-api-destinations.md) per account per Region\. API destinations are HTTP invocation endpoints that you can use as the target for a rule\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Rate of invocations per API destination  |  Default: 300 TPS\. The maximum number of invocations per second to send to each API destination endpoint per account per Region\. Once the quota is met, future invocations to that API endpoint are throttled\. The invocations will still occur, but are delayed\. This soft limit prevents your endpoint from being overloaded with event traffic\. If your endpoint can handle more event traffic, use the [Service Quota console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) to raise this limit\.  | 
|  Connections  |  Default: 3,000\. The maximum number of connections per account per Region\. Connections are used with API destination to define the authorization parameters to use to connect to the destination HTTP endpoint\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Event buses  |  Maximum 100 event buses per account\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Event buses \- other quotas  |  There's no restriction on the rate of [events](eb-events.md) that can be received from AWS services or other AWS accounts\. If you send custom events to your [event bus](eb-event-bus.md) using the `PutEvents` API, the `PutEvents` [API quotas](#eb-putevents-limits) apply\. Any events that are sent to the targets of the rules in your account count against your invocations quota\. This includes cross\-account and cross\-Region event delivery\. The policy size of an event bus is limited to 10240 characters\. This policy size increases each time you grant access to another account\. You can see your current policy and its size by using the `DescribeEventBus` API\.  The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Event pattern  |  2048 characters maximum\.  | 
|  Invocations  |  An invocation is an event matching a rule and being sent to the rule’s targets\. Quotas vary by region\. See [Invocation quotas by Region](#eb-invocations-limits)\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  [ListRuleNamesByTarget](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)  |  Maximum 100 results per page for requests\.  | 
|  [ListRules](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)  |  Maximum 100 results per page for requests\.  | 
|  [ListTargetsByRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)  |  Maximum 100 results per page for requests\.  | 
| [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) entry size | Maximum 256KB | 
|  [PutTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)  |  10 entries per request\. Up to 5 targets per rule\.  | 
|  [RemoveTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)  |  10 entries per request\.  | 
|  Rules  |  300 per event bus\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. Before increasing quotas, examine your rules\. If you have multiple rules that match very specific events, consider broadening their scope by using fewer identifiers in your [Amazon EventBridge events](eb-events.md)\. In addition, consider adding more targets to your rules\.  | 
|  Schema discovery  |  Schema discovery will only infer events that are nested up to 255 levels\. Any events past 255 levels are ignored\.\.  | 
| Systems Manager Run Command target | 1 target key and 1 target value Systems Manager Run Command doesn't currently support multiple target values\.  | 
|  Targets  |  Maximum 5 targets per rule\.  | 

## PutEvents quotas by Region<a name="eb-putevents-limits"></a>

The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. For quotas above 100,000 TPS, our service team will host a call to best support you 

**Note**  
Events sent to a different Region using PutEvents count towards the PutEvents quota of the destination Region for the account that owns the role used to send the events\.


| Regions | Transactions per second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 10,000 requests per second by default in these Regions\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 2,400 requests per second by default in these Regions\.  | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 1,200 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 600 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 400 requests per second by default in these Regions\. | 

## PutPartnerEvents quotas by Region<a name="eb-putpartnervents-limits"></a>

To request quota increases, please [contact support](https://console.aws.amazon.com/support/home?#/case/create?issueType=technical)\.


| Regions | Transactions per second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  [PutPartnerEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutPartnerEvents.html) has a soft limit of 1,400 throughput requests per second and 3,600 burst requests per second by default in all Regions\.  | 

## Invocation quotas by Region<a name="eb-invocations-limits"></a>

An invocation is an event matching a rule and then being sent to the rule’s targets\. If the invocation of a target fails due to a problem with the target service or account throttling, EventBridge reattempts for up to 24 hours\.

If you receive events from another account, each event that matches a rule in your account and is sent to the rule’s targets counts against your account’s quota of invocations per second\.

After your reach the invocation quota in your region, EventBridge throttles invocations\. They still happen but are delayed\.

The Service Quotas console provides information about EventBridge quotas\. You can also use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. For quotas above 100,000 TPS, our service team will set up a call to best support you\.


| Regions | Invocations per second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html)  |  The invocations quota has a soft limit of 18,750 requests per second by default in these Region\.  | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | The invocations quota has a soft limit of 4,500 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | The invocations quota has a soft limit of 2,250 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | The invocations quota has a soft limit of 1,100 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-quota.html) | The invocations quota has a soft limit of 750 requests per second by default in these Regions\. | 