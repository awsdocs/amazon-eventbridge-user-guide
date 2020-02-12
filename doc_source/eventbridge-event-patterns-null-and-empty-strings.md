# Matching Null Values and Empty Strings in EventBridge Event Patterns<a name="eventbridge-event-patterns-null-and-empty-strings"></a>

You can create a pattern that matches an event field that has a null value or an empty string\. To see how this works, consider the following example event:

```
{
  "version": "0",
  "id": "3e3c153a-8339-4e30-8c35-687ebef853fe",
  "detail-type": "EC2 Instance Launch Successful",
  "source": "aws.autoscaling",
  "account": "123456789012",
  "time": "2015-11-11T21:31:47Z",
  "region": "us-east-1",
  "resources": [
   ],
  "detail": {
    "eventVersion": "",
    "responseElements": null
   }
}
```

To match events where the value of `eventVersion` is an empty string, use the following pattern, which would match the event example\.

```
{
  "detail": {
     "eventVersion": [""]
  }
}
```

To match events where the value of `responseElements` is null, use the following pattern, which would match the event example\.

```
{
  "detail": {
     "responseElements": [null]
  }
}
```

Null values and empty strings are not interchangeable in pattern matching\. A pattern that is written to detect empty strings will not catch values of `null`\.