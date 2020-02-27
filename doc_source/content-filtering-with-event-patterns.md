# Content\-based Filtering with Event Patterns<a name="content-filtering-with-event-patterns"></a>

Amazon EventBridge supports declarative filtering using event patterns\. With event pattern content filtering you can write complex rules that only trigger under very specific conditions\. For instance, you might want a rule that will trigger only when a field of the event is within a specific numeric range, if the event comes from a specific IP address, or only if a specific field does not exist in the event JSON\. Content filtering allows you to create complex rules in your event patterns, so that the rule will only call a target if your filtering conditions are met\.

**Topics**
+ [Prefix Matching](#filtering-prefix-matching)
+ [Anything\-but Matching](#filtering-anything-but)
+ [Numeric Matching](#filtering-numeric-matching)
+ [IP Address Matching](#filtering-ip-matching)
+ [Exists Matching](#filtering-exists-matching)
+ [Complex Example with Multiple Matching](#filtering-complex-example)

## Prefix Matching<a name="filtering-prefix-matching"></a>

You can match on the prefix of a value in the event source\. For example, the following event pattern would match on any event where the `"time"` field started with `"2017-10-02"`\. 

```
{
  "time": [ { "prefix": "2017-10-02" } ],
}
```

The above event pattern would match on any event with that date in the time field, including `"time": "2017-10-02T18:43:48Z"`\.

**Note**  
Prefix matching only works on string\-valued fields\.

### Prefix Matching Example<a name="filtering-prefix-matching-example"></a>

Suppose you want to process all AWS Auto Scaling events from European regions\. The following event pattern shows how to matches on that\.

```
{
  "source": [ "aws.autoscaling" ],
  "region": [ { "prefix": "eu-" } ]
}
```

## Anything\-but Matching<a name="filtering-anything-but"></a>

Anything\-but matching matches anything except what's provided in the rule\.

You can use `anything-but` with strings and numeric values, including lists that contain only strings, or only numbers\.

The following shows single `anyting-but` matching, first with strings and then with numbers\.

```
{
  "detail": {
    "state": [ { "anything-but": "initializing" } ]
  }
}

{
  "detail": {
    "x-limit": [ { "anything-but": 123 } ]
  }
}
```

The following shows `anything-but` matching with a list of strings\.

```
{
  "detail": {
    "state": [ { "anything-but": [ "stopped", "overloaded" ] } ]
  }
}
```

The following shows `anything-but` matching with a list of numbers\.

```
{
  "detail": {
    "x-limit": [ { "anything-but": [ 100, 200, 300 ] } ]
  }
}
```

The following shows an `anything-but` event pattern that matches on a prefix\. It will match on any event, except those that have the prefix `init` for the `"state"` field\.

```
{
  "detail": {
    "state": [ { "anything-but": { "prefix": "init" } } ]
  }
}
```

### Anything\-but Matching Example<a name="filtering-anything-but-matching-example"></a>

Sometimes you want to exclude rather than include a particular field value\. Suppose you want to process all events except those that are AWS CloudTrail reports of API calls\.

```
{
  "detail-type": [ { "anything-but": "AWS API Call via CloudTrail" } ]
}
```

The `anything-but` match expression can blacklist literal strings or also a list of values\. The list must contain either all strings, or all numbers\. To see all the events except those that came from Amazon EC2 or Amazon S3, do the following\.

```
{
  "source": [ { "anything-but": [ "aws.ec2", "aws.s3" ] } ]
}
```

The `anything-but` match expression can also use a nested match expression to exclude prefixes\. For example, the EventBridge main event bus has a large number of events coming from all AWS services\. But, you can also inject your own events using the [PutEvents](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEvents.html) API\. You can distinguish AWS events and process only your own because the `“source”` field in all AWS events begins with the string "`aws.`"\.

```
{
  "source": [ { "anything-but": { "prefix": "aws." } } ]
}
```

## Numeric Matching<a name="filtering-numeric-matching"></a>

The following shows numeric matching for an event pattern\. 

```
{
  "detail": {
    "c-count": [ { "numeric": [ ">", 0, "<=", 5 ] } ],
    "d-count": [ { "numeric": [ "<", 10 ] } ],
    "x-limit": [ { "numeric": [ "=", 3.018e2 ] } ]
  }
}
```

This pattern will only match evaluations that are true for all the fields\. Numeric matching only works with values that are JSON numbers, and is limited to values between \-1\.0e9 and \+1\.0e9 inclusive, with 15 digits of precision \(six digits to the right of the decimal point\)\.

## IP Address Matching<a name="filtering-ip-matching"></a>

IP address matching is available for both IPv4 and IPv6 addresses\.

```
{
  "detail": {
    "source-ip": [ { "cidr": "10.0.0.0/24" } ]
  }
}
```

## Exists Matching<a name="filtering-exists-matching"></a>

Exists matching works on the presence or absence of a field in the JSON of the event\.

The following event pattern will match any event which does not have a `detail.c-count` field\.

```
{
  "detail": {
    "c-count": [ { "exists": false  } ]
  }
}
```

**Note**  
Exists matching **only works on leaf nodes**\. It does not work on intermediate nodes\.

For example, the above pattern would match the following event\.

```
{
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "resources": [ "arn:aws:ec2:us-east-1:123456789012:instance/i-02ebd4584a2ebd341" ],
  "detail": {
    "state": [ "initializing", "running" ]
  }
}
```

But, it would also match the following event because `c-count` is not a leaf node\.

```
{
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "resources": [ "arn:aws:ec2:us-east-1:123456789012:instance/i-02ebd4584a2ebd341" ],
  "detail": {
    "state": [ "initializing", "running" ]
    "c-count" : {
       "c1" : 100
    }
  }
}
```

### Existence Matching Example<a name="filtering-existence-matching-example"></a>

Suppose you wanted to make an Elasticsearch full\-text index of a bunch of events\. To do this, you select all the events that have a description field as follows\.

```
{
  "detail": {
  "description": [ { "exists": true } ],
  }
}
```

You could also use `{ "exists": false }` to select events that do not contain a particular field\.

## Complex Example with Multiple Matching<a name="filtering-complex-example"></a>

You can combine multiple matching rules into a more complex event pattern\. For example, the following combines `anything-but` and `numeric` in a single event pattern\.

```
{
  "time": [ { "prefix": "2017-10-02" } ],
  "detail": {
    "state": [ { "anything-but": "initializing" } ],
    "c-count": [ { "numeric": [ ">", 0, "<=", 5 ] } ],
    "d-count": [ { "numeric": [ "<", 10 ] } ],
    "x-limit": [ { "anything-but": [ 100, 200, 300 ] } ]
  }
}
```