# Content filtering in Amazon EventBridge event patterns<a name="eb-event-patterns-content-based-filtering"></a>

Amazon EventBridge supports declarative content filtering using [event patterns](eb-event-patterns.md)\. With content filtering, you can write complex event patterns that only match events under very specific conditions\. For example, you can create an event pattern that matches an event when a field of the [event](eb-events.md) is within a specific numeric range, if the event comes from a specific IP address, or only if a specific field doesn't exist in the event JSON\. 

**Important**  
In EventBridge, it is possible to create rules that lead to infinite loops, where a rule is fired repeatedly\. For example, a rule might detect that ACLs have changed on an S3 bucket, and trigger software to change them to the desired state\. If the rule is not written carefully, the subsequent change to the ACLs fires the rule again, creating an infinite loop\.  
To prevent this, write the rules so that the triggered actions do not re\-fire the same rule\. For example, your rule could fire only if ACLs are found to be in a bad state, instead of after any change\.   
An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified limit\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

**Topics**
+ [Prefix matching](#eb-filtering-prefix-matching)
+ [Anything\-but matching](#eb-filtering-anything-but)
+ [Numeric matching](#filtering-numeric-matching)
+ [IP address matching](#eb-filtering-ip-matching)
+ [Exists matching](#eb-filtering-exists-matching)
+ [Complex example with multiple matching](#eb-filtering-complex-example)

## Prefix matching<a name="eb-filtering-prefix-matching"></a>

You can match an event depending on the prefix of a value in the event source\. You can use prefix matching for string values\.

For example, the following event pattern would match any event where the `"time"` field started with `"2017-10-02"` such as `"time": "2017-10-02T18:43:48Z"`\. 

```
{
  "time": [ { "prefix": "2017-10-02" } ]
}
```

## Anything\-but matching<a name="eb-filtering-anything-but"></a>

*Anything\-but* matching matches anything except what's provided in the rule\.

You can use anything\-but matching with strings and numeric values, including lists that contain only strings, or only numbers\.

The following event pattern shows anything\-but matching with strings and numbers\.

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

The following event pattern shows anything\-but matching with a list of strings\.

```
{
  "detail": {
    "state": [ { "anything-but": [ "stopped", "overloaded" ] } ]
  }
}
```

The following event pattern shows anything\-but matching with a list of numbers\.

```
{
  "detail": {
    "x-limit": [ { "anything-but": [ 100, 200, 300 ] } ]
  }
}
```

The following event pattern shows anything\-but matching that matches any event that doesn't have the prefix `"init"` in the `"state"` field\.

**Note**  
*Anything\-but* matching only works with a single prefix, not a list\.

```
{
  "detail": {
    "state": [ { "anything-but": { "prefix": "init" } } ]
  }
}
```

## Numeric matching<a name="filtering-numeric-matching"></a>

Numeric matching works with values that are JSON numbers\. It is limited to values between \-1\.0e9 and \+1\.0e9 inclusive, with 15 digits of precision, or six digits to the right of the decimal point\.

The following shows numeric matching for an event pattern that only matches events that are true for all fields\. 

```
{
  "detail": {
    "c-count": [ { "numeric": [ ">", 0, "<=", 5 ] } ],
    "d-count": [ { "numeric": [ "<", 10 ] } ],
    "x-limit": [ { "numeric": [ "=", 3.018e2 ] } ]
  }
}
```

## IP address matching<a name="eb-filtering-ip-matching"></a>

You can use IP address matching for IPv4 and IPv6 addresses\. The following event pattern shows IP address matching to IP addresses that start with 10\.0\.0 and end with a number between 0 and 255\.

```
{
  "detail": {
    "sourceIPAddress": [ { "cidr": "10.0.0.0/24" } ]
  }
}
```

## Exists matching<a name="eb-filtering-exists-matching"></a>

*Exists matching* works on the presence or absence of a field in the JSON of the event\.

Exists matching only works on leaf nodes\. It does not work on intermediate nodes\.

The following event pattern matches any event that has a `detail.state` field\.

```
{
  "detail": {
    "state": [ { "exists": true  } ]
  }
}
```

The preceding event pattern matches the following event\.

```
{
  "version": "0",
  "id": "7bf73129-1428-4cd3-a780-95db273d1602",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "2015-11-11T21:29:54Z",
  "region": "us-east-1",
  "resources": ["arn:aws:ec2:us-east-1:123456789012:instance/i-abcd1111"],
  "detail": {
    "instance-id": "i-abcd1111",
    "state": "pending"
  }
}
```

The preceding event pattern does NOT match the following event because it doesn't have a `detail.state` field\.

```
{
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "resources": [ "arn:aws:ec2:us-east-1:123456789012:instance/i-02ebd4584a2ebd341" ],
  "detail": {
    "c-count" : {
       "c1" : 100
    }
  }
}
```

## Complex example with multiple matching<a name="eb-filtering-complex-example"></a>

You can combine multiple matching rules into a more complex event pattern\. For example, the following event pattern combines `anything-but` and `numeric`\.

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

**Note**  
When building event patterns, if you include a key more than once the last reference will be the one used to evaluate events\. For example, for the following pattern:  

```
{
  "detail": {
    "location": [ { "prefix": "us-" } ],
    "location": [ { "anything-but": "us-east" } ]
  }
}
```
only `{ "anything-but": "us-east" }` will be taken into account when evaluating the `location`\.