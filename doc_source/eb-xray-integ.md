# Amazon EventBridge integration with AWS X\-Ray<a name="eb-xray-integ"></a>

AWS X\-Ray integrates with Amazon EventBridge to trace events that are passed through EventBridge\. If a service traces requests by using the X\-Ray SDK, the trace header is passed through EventBridge and the original trace header propogates to the invocation targets with consistent trace context\. Trace continuity enables users to track, analyze, and debug throughout downstream services\.

Trace headers are validated against X\-Ray’s validation logic\. Trace headers that don't satisfy X\-Ray’s validation logic will be dropped and not propagated\. However, the events will still be processed\.

The trace header will **not** be available on the event that's delivered to the invocation target\. This means:
+ If you have an event archive, the trace header won't be available on archived events\. If you replay archived events the trace header won't be included\.
+ If you have configured a dead\-letter queue \(DLQ\) to send events with failed invocations, the trace header is included in the `SendMessage` request that sends the event to the DLQ\. If you retrieve events \(messages\) from the DLQ using `ReceiveMessage`, the trace header associated with the event is included on the Amazon SQS message attribute but is not included in the event payload \(message body\)\.

To see how an EventBridge event node connects source and target services, see [Viewing source and targets in the X\-Ray service map](https://docs.aws.amazon.com/xray/latest/devguide/xray-services-eventbridge.html#xray-services-eventbridge-service-map)\.

EventBridge supports the following trace header information:
+ **Default HTTP Header –** The X\-Ray SDK automatically populates the trace header as the `X-Amzn-Trace-Id` HTTP header for all invocation targets\. To learn more about the default HTTP header, see [Tracing header](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html#xray-concepts-tracingheader)\.
+ **`TraceHeader` System Attribute –** `TraceHeader` is a [PutEventsRequestEntry attribute](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEventsRequestEntry.html) reserved by EventBridge to carry the X\-Ray trace header with events to invocation targets\. When both header instrumentations are set, the `PutEventsRequestEntry` attribute overrides the HTTP trace header\. 

**Note**  
The trace header won’t count towards the `PutEventsRequestEntry` event size calculation, regardless of whether it is defined as an HTTP header or as a `PutEventsRequestEntry` attribute\. To learn more, see [Calculating PutEvents Event Entry Sizes](calculate-putevents-entry-size.md)\.