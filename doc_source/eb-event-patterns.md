# Amazon EventBridge event patterns<a name="eb-event-patterns"></a>

Event patterns have the same structure as the [events](eb-events.md) they match\. [Rules](eb-rules.md) use event patterns to select events and send them to targets\. An event pattern either matches an event or it doesn't\.

**Topics**
+ [Create event patterns](#eb-create-pattern)
+ [Example events and event patterns](#eb-filtering-data-types)
+ [Matching null values and empty strings in Amazon EventBridge event patterns](eb-event-patterns-null-values.md)
+ [Arrays in Amazon EventBridge event patterns](eb-event-patterns-arrays.md)
+ [Content filtering in Amazon EventBridge event patterns](eb-event-patterns-content-based-filtering.md)

The following event shows a simple AWS event from Amazon EC2\.

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
    "arn:aws:ec2:us-west-1:123456789012:instance/i-1234567890abcdef0"
  ],
  "detail": {
    "instance-id": "i-1234567890abcdef0",
    "state": "terminated"
  }
}
```

The following event pattern processes all Amazon EC2 `instance-termination` events\.

```
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["terminated"]
  }
}
```

## Create event patterns<a name="eb-create-pattern"></a>

To create an event pattern, you specify the fields of an event that you want the event pattern to match\. Only specify the fields that you use for matching\. The previous event pattern example only provides values for three fields: the top\-level fields `“source”` and `“detail-type”`, and the `“state”` field inside the `“detail”` object field\. EventBridge ignores all the other fields in the event when applying the rule\.



For an event pattern to match an event, the event must contain all the field names listed in the event pattern\. The field names must also appear in the event with the same nesting structure\.

EventBridge ignores the fields in the event that aren't included in the event pattern \. The effect is that there is a "\*": "\*" wildcard for fields that don't appear in the event pattern\.

The values that event patterns match follow JSON rules\. You can include strings enclosed in quotation marks \("\), numbers, and the keywords `true`, `false`, and `null`\.

For strings, EventBridge uses exact character\-by\-character matching without case\-folding or any other string normalization\.

For numbers, EventBridge uses string representation\. For example, 300, 300\.0, and 3\.0e2 are not considered equal\.

When you write event patterns to match events, you can use the `TestEventPattern` API or the `test-event-pattern` CLI command to test that your pattern matches the correct events\. For more information, see [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html)\.

Here's a summary of all the comparison operators available in EventBridge:


| **Comparison** | **Example** | **Rule syntax** | 
| --- | --- | --- | 
|  Null  |  UserID is null  |  “UserID”: \[ null \]  | 
|  Empty  |  LastName is empty  |  “LastName”: \[“”\]  | 
|  Equals  |  Name is “Alice”  |  “Name”: \[ “Alice” \]  | 
|  And  |  Location is “New York” and Day is “Monday”  |  “Location”: \[ “New York” \], “Day”: \[“Monday”\]  | 
|  Or  |  PaymentType is “Credit” or “Debit”  |  “PaymentType”: \[ “Credit”, “Debit”\]  | 
|  Not  |  Weather is anything but “Raining”  |  “Weather”: \[ \{ “anything\-but”: \[ “Raining” \] \} \]  | 
|  Numeric \(equals\)  |  Price is 100  |  “Price”: \[ \{ “numeric”: \[ “=”, 100 \] \} \]  | 
|  Numeric \(range\)  |  Price is more than 10, and less than or equal to 20  |  “Price”: \[ \{ “numeric”: \[ “>”, 10, “≤", 20 \] \} \]  | 
|  Exists  |  ProductName exists  |  “ProductName”: \[ \{ “exists”: true \} \]  | 
|  Does not exist  |  ProductName does not exist  |  “ProductName”: \[ \{ “exists”: false \} \]  | 
|  Begins with  |  Region is in the US  |  “Region”: \[ \{“prefix”: “us\-“ \} \]  | 

### Match Values<a name="eb-filtering-match-values"></a>

In an event pattern, the value to match is in a JSON array, surrounded by square brackets \(“\[”, “\]”\) so that you can provide multiple values\. For example, to match events from Amazon EC2 or AWS Fargate, you could use the following pattern, which matches events where the value for the `"source"` field is either `"aws.ec2"` or `"aws.fargate"`\.

```
{
    "source": ["aws.ec2", "aws.fargate"]
}
```



## Example events and event patterns<a name="eb-filtering-data-types"></a>

You can use all of the JSON data types and values to match events\. The following examples show events and the event patterns that match them\.

### Field matching<a name="eb-filtering-example"></a>

You can match on the value of a field\. Consider the following Amazon EC2 Auto Scaling event\.

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

For the preceding event, you can use the `“responseElements”` field to match\.

```
{
  "source": ["aws.autoscaling"],
  "detail-type": ["EC2 Instance Launch Successful"],
  "detail": {
   "responseElements": [null]
  }
}
```

### Value matching<a name="eb-filtering-value-example"></a>

Consider the following Amazon Macie Classic event, which is truncated\.

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
    "alert-arn": "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id/alert/alert_",
    "risk-score": 80,
    "trigger": {
      "rule-arn": "arn:aws:macie:us-east-1:123456789012:trigger/trigger_id",
      "alert-type": "basic",
      "created-at": "2017-01-02 19:54:00.644000",
      "description": "Alerting on failed enumeration of large number of bucket policies",
      "risk": 8
    },
"created-at": "2017-04-18T00:21:12.059000",
.
.
.
```

The following event pattern matches any event that has a risk score of 80 and a trigger risk of 8\.

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
