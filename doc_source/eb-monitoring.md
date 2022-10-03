# Monitoring Amazon EventBridge<a name="eb-monitoring"></a>

EventBridge sends metrics to Amazon CloudWatch every minute for everything from the number of matched [events](eb-events.md) to the number of times a [target](eb-targets.md) is invoked by a [rule](eb-rules.md)\.



**Topics**
+ [EventBridge metrics](#eb-metrics)
+ [Dimensions for EventBridge metrics](#eb-metrics-dimensions)

## EventBridge metrics<a name="eb-metrics"></a>

The `AWS/Events` namespace includes the following metrics\.

 All of these metrics use Count as the unit, so Sum and SampleCount are the most useful statistics\.


| Metric | Description | 
| --- | --- | 
|  `DeadLetterInvocations`  |  The number of times a rule’s target isn't invoked in response to an event\. This includes invocations that would result in running the same rule again, causing an infinite loop\. Valid Dimensions: RuleName Units: Count  | 
|  `Events`  |  The number of partner events ingested by EventBridge\. Valid Dimensions: EventSourceName Units: Count  | 
|  `FailedInvocations`  |  The number of invocations that failed permanently\. This doesn't include invocations that are retried or invocations that succeeded after a retry attempt\. It also doesn't count failed invocations that are counted in `DeadLetterInvocations`\. Valid Dimensions: RuleName Units: Count  | 
|  `IngestionToInvocationStartLatency`  |  The time to process events measured from when they're ingested by EventBridge to the first invocation of a target in your rules\. This is a service\-level metric measured across all of your rules and buses and provides an indication of the health of the EventBridge service\. Any extended periods of high latency over 30 seconds may indicate a service disruption\. Valid Dimensions: None Units: Milliseconds  | 
|  `Invocations`  |  The number of times a target is invoked by a rule in response to an event\. This includes successful and failed invocations, but doesn't include throttled or retried attempts until they fail permanently\. It doesn't include `DeadLetterInvocations`\.  EventBridge only sends this metric to CloudWatch if it isn't zero\.  Valid Dimensions: RuleName Units: Count  | 
|  `InvocationsFailedToBeSentToDlq`  |  The number of invocations that couldn't be moved to a dead\-letter queue\. Dead\-letter queue errors occur due to permissions errors, unavailable resources, or size limits\.  EventBridge only sends this metric to CloudWatch if it isn't zero\.  Valid Dimensions: RuleName Units: Count  | 
|  `InvocationsSentToDlq`  |  The number of invocations that are moved to a dead\-letter queue\.  EventBridge only sends this metric to CloudWatch if it isn't zero\.  Valid Dimensions: RuleName Units: Count  | 
|  `MatchedEvents`  |  The number of events that matched with any rule\. Valid Dimensions: None Units: Count  | 
|  `ThrottledRules`  |  The number of rules that have tried to run but are being throttled\. Valid Dimensions: RuleName Units: Count  | 
|  `TriggeredRules`  |  The number of rules that have run and matched with any event\. You won't see this metric in CloudWatch until a rule is triggered\. Valid Dimensions: RuleName Units: Count  | 

## Dimensions for EventBridge metrics<a name="eb-metrics-dimensions"></a>

EventBridge metrics have *dimensions*, or sortable attributes, which are listed below\.


|  Dimension  |  Description  | 
| --- | --- | 
|  EventBusName  |  Filters the available metrics by event bus name\.  | 
|  EventSourceName  |  Filters the available metrics by partner event source name\.  | 
|  RuleName  |  Filters the available metrics by rule name\.  | 