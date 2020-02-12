# Event Patterns<a name="filtering-examples-structure"></a>

Event patterns have the same structure as the Events they match\. They look much like the events they are filtering\. Rules use event patterns to select events and route them to targets\. A pattern either matches an event or it doesn't\. The following is an example of a simple AWS Event which you might encounter on EventBridge\.

```
{
  "version": "0",
  "id": "6a7e8feb-b491-4cf7-a9f1-bf3703467718",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "111122223333",
  "time": "2017-12-22T18:43:48Z",
  "region": "us-west-1",
  "resources": [
    "arn:aws:ec2:us-west-1:123456789012:instance/ i-1234567890abcdef0"
],
"detail": {
  "instance-id": " i-1234567890abcdef0",
  "state": "terminated"
  }
}
```

Event patterns have the same structure as the events they match\. For example, the following event pattern allows you to subscribe to only events from Amazon EC2\.

```
{
  "source": [ "aws.ec2" ]
}
```

The pattern simply quotes the fields you want to match and provides the values you are looking for\.

The sample event above, like most events, has a nested structure\. Suppose you want to process all `instance-termination` events\. Create an event pattern like the following\.

```
{
  "source": [ "aws.ec2" ],
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "detail": {
    "state": [ "terminated" ]
  }
}
```

## Specify Fields to Match<a name="filtering-examples-specify-fields"></a>

Only specify fields that you care about\. In the previous example, you only provide values for three fields: The top\-level fields `“source” ` and `“detail-type”`, and the `“state”` field inside the `“detail”` object field\. EventBridge ignores all the other fields in the event while applying the filter\.

## Match Values<a name="filtering-match-values"></a>

Match values are always in arrays\. Note that the value to match is in a JSON array, surrounded by “\[” and “\]”\. This is so you can provide multiple values\. For example, if you were interested in events from Amazon EC2 or Fargate, you could specify the following\.

```
{
    "source": [ "aws.ec2", "aws.fargate" ]
}
```

This matches on events where the value for the `"source"` field is either `"aws.ec2"` or `"aws.fargate"`\.

## Match on All JSON Data Types<a name="filtering-data-types"></a>

You can match on all of the JSON data types\. Consider the following example Amazon EC2 Auto Scaling event\.

```
{
  "version": "0",
  "id": "3e3c153a-8339-4e30-8c35-687ebef853fe",
  "detail-type": "EC2 Instance Launch Successful",
  "source": "aws.autoscaling",
  "account": "123456789012",
  "time": "2015-11-11T21:31:47Z",
  "region": "us-east-1",
  "resources": [],
  "detail": {
    "eventVersion": "",
    "responseElements": null
  }
}
```

For the above example, you can match on the ` “responseElements”` field as follows\.

```
{
  "source": [ "aws.autoscaling" ],
  "detail-type": [ "EC2 Instance Launch Successful" ],
  "detail": {
   "responseElements": [ null ]
  }
}
```

This works for numbers too\. Consider the following Amazon Macie event \(truncated for brevity\)\.

```
{
  "version": "0",
  "id": "3e355723-fca9-4de3-9fd7-154c289d6b59",
  "detail-type": "Macie Alert",
  "source": "aws.macie",
  "account": "123456789012",
  "time": "2017-04-24T22:28:49Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id/alert/alert_id",
    "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id"
  ],
  "detail": {
  "notification-type": "ALERT_CREATED",
  "name": "Scanning bucket policies",
  "tags": [
  "Custom_Alert",
  "Insider"
  ],
  "url": "https://lb00.us-east-1.macie.aws.amazon.com/111122223333/posts/alert_id",
  "alert-arn": "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id/alert/alert_
  "risk-score": 80,
  "trigger": {
    "rule-arn": "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id",
    "alert-type": "basic",
    "created-at": "2017-01-02 19:54:00.644000",
    "description": "Alerting on failed enumeration of large number of bucket policie
    "risk": 8
},
"created-at": "2017-04-18T00:21:12.059000",
.
.
.
```

If you want to match anything that has a risk score of 80 and a trigger risk of 8, do the following\.

```
{
  "source": ["aws.macie"],
  "detail-type": ["Macie Alert"],
  "detail": {
    "risk-score": [80],
    "trigger": {
      "risk": [8]
    }
  }
}
```

## Simple Matching with Event Patterns<a name="eventbridge-event-patterns"></a>

The following example event is used to show how the subsequent event patterns would match with this event JSON\.

```
{
  "version": "0",
  "id": "6a7e8feb-b491-4cf7-a9f1-bf3703467718",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "111122223333",
  "time": "2017-12-22T18:43:48Z",
  "region": "us-west-1",
  "resources": [
    "arn:aws:ec2:us-west-1:123456789012:instance/ i-1234567890abcdef0"
  ],
  "detail": {
    "instance-id": " i-1234567890abcdef0",
    "state": "terminated"
  }
}
```

Event patterns are represented as JSON objects with a structure that is similar to that of events, for example:

```
{
  "source": [ "aws.ec2" ],
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "detail": {
    "state": [ "running" ]
  }
}
```

This event pattern would not match on the example event, as the value of the `"state"` field matches on `"running"`, but the value in the example event is `"terminated"`\.

It is important to remember the following about event pattern matching:
+ For a pattern to match an event, the event must contain all the field names listed in the pattern\. The field names must appear in the event with the same nesting structure\.
+ Other fields of the event not mentioned in the pattern are ignored; effectively, there is a "\*": "\*" wildcard for fields not mentioned\.
+ The matching is exact \(character\-by\-character\), without case\-folding or any other string normalization\.
+ The values being matched follow JSON rules: Strings enclosed in quotes, numbers, and the unquoted keywords `true`, `false`, and `null`\.
+ Number matching is at the string representation level\. For example, 300, 300\.0, and 3\.0e2 are not considered equal\.

When you write patterns to match events, you can use the `TestEventPattern` API or the `test-event-pattern` CLI command to make sure that your pattern will match the desired events\. For more information, see [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html) or [test\-event\-pattern](https://docs.aws.amazon.com/cli/latest/reference/events/test-event-pattern.html)\.

The following event patterns would match the previous example event\. The first pattern matches because one of the instance values specified in the pattern matches the event \(and the pattern does not specify any additional fields not contained in the event\)\. The second one matches because the "terminated" state is contained in the event\.

```
{
  "resources": [
    "arn:aws:ec2:us-east-1:123456789012:instance/i-12345678",
    "arn:aws:ec2:us-east-1:123456789012:instance/i-abcdefgh"
  ]
}
```

```
{
  "detail": {
    "state": [ "terminated" ]
  }
}
```

These event patterns do not match the event at the top of this page\. The first pattern does not match because the pattern specifies a "pending" value for state, and this value does not appear in the event\. The second pattern does not match because the resource value specified in the pattern does not appear in the event\. 

```
{
  "source": [ "aws.ec2" ],
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "detail": {
    "state": [ "pending" ]
  }
}
```

```
{
  "source": [ "aws.ec2" ],
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "resources": [ "arn:aws:ec2:us-east-1::image/ami-12345678" ]
}
```