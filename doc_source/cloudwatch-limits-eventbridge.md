# Amazon EventBridge Quotas<a name="cloudwatch-limits-eventbridge"></a>

EventBridge has the following quotas\.


| Resource | Default Limit | 
| --- | --- | 
|  Event Publishing API requests  |  [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) operations are limited based on AWS region\. See [PutEvents Quotas by Region](#putevents-limits)\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  All other API requests  |  All EventBridge API other than `PutEvents` are limited to 50 requests per second by default\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Event buses  |  Up to 100 event buses per account\.  | 
|  Event buses \- other quotas  |  There is no restriction on the rate of events that can be received from AWS services or other AWS accounts\. If you send custom events to your event bus using the `PutEvents` API, the `PutEvents` [API quotas](#putevents-limits) apply\. Any events that are sent on to the targets of the rules in your account count against your invocations quota\.  The policy size of an event bus is limited to 10240 characters\. This policy size increases each time you grant access to another account\. You can see your current policy and its size by using the `DescribeEventBus` API\.  The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Event pattern  |  2048 characters maximum\.  | 
|  Invocations  |  An invocation is an event matching a rule and being sent on to the rule’s targets\. Quotas vary by region\. See [Invocation Quotas by Region](#invocations-limits)\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Rate of invocations per API destination  |  Default: 300 TPS\. The maximum number of invocations per second to send to each API destination endpoint per account per Region\. Once the quota is met, future invocations to that API endpoint are throttled\. The invocations will still occur, but are delayed\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  Connections  |  Default: 3,000 The maximum number of connections per account per Region\. Connections are used with API destination to define the authorization parameters to use to connect to the destination HTTP endpoint\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  API destinations  |  Default: 3,000 The maximum number of API destinations per account per Region\. API destinations are HTTP invocation endpoints you can use as the target for a rule\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\.  | 
|  [ListRuleNamesByTarget](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRuleNamesByTarget.html)  |  Up to 100 results per page for requests\.  | 
|  [ListRules](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListRules.html)  |  Up to 100 results per page for requests\.  | 
|  [ListTargetsByRule](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTargetsByRule.html)  |  Up to 100 results per page for requests\.  | 
|  [PutTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutTargets.html)  |  10 entries per request\. Up to 5 targets per rule\.  | 
|  [RemoveTargets](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_RemoveTargets.html)  |  10 entries per request\.  | 
|  Rules  |  300 per event bus\. The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. Before increasing quotas, examine your rules\. You may have multiple rules each matching to very specific events\. Consider broadening their scope by using fewer identifiers in your [Events and Event Patterns in EventBridge](eventbridge-and-event-patterns.md)\. In addition, a rule can invoke several targets each time it matches an event\. Consider adding more targets to your rules\.  | 
| Systems Manager Run Command target | 1 target key and 1 target value Systems Manager Run Command does not currently support multiple target values\.  | 
|  Targets  |  Up to 5 targets per rule\.  | 

## PutEvents Quotas by Region<a name="putevents-limits"></a>

The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. For quotas above 100,000 TPS, our service team will setup a call to best support your use case\.


| Regions | Transactions per Second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html)  |  [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 10,000 requests per second by default in this Region\.  | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 2,400 requests per second by default in these Regions\.  | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 1,200 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 600 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) has a soft limit of 400 requests per second by default in these Regions\. | 

## Invocation Quotas by Region<a name="invocations-limits"></a>

An invocation is an event matching a rule and being sent on to the rule’s targets\. If the invocation of a target fails due to a problem with the target service, account throttling, etc\., new attempts are made for up to 24 hours for a specific invocation\.

If you are receiving events from another account, each of those events that matches a rule in your account and is sent on to the rule’s targets counts against your account’s quota of invocations per second\.

After your reach the one of the following invocation quotas in your region invocations are throttled\. They still happen but they are delayed\.

The Service Quotas console provides information about EventBridge quotas\. Along with viewing the default quotas, you can use the Service Quotas console to [request quota increases](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/services/events/quotas) for adjustable quotas\. For quotas above 100,000 TPS, our service team will setup a call to best support your use case\.


| Regions | Invocations per Second | 
| --- | --- | 
|  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html)  |  The invocations quota has a soft limit of 18,750 requests per second by default in this Region\.  | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | The invocations quota has a soft limit of 4,500 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | The invocations quota has a soft limit of 2,250 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | The invocations quota has a soft limit of 1,100 requests per second by default in these Regions\. | 
| [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/cloudwatch-limits-eventbridge.html) | The invocations quota has a soft limit of 750 requests per second by default in these Regions\. | 