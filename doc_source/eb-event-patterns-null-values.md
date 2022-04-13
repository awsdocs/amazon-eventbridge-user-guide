# Matching null values and empty strings in Amazon EventBridge event patterns<a name="eb-event-patterns-null-values"></a>

**Important**  
In EventBridge, it is possible to create rules that lead to infinite loops, where a rule is fired repeatedly\. For example, a rule might detect that ACLs have changed on an S3 bucket, and trigger software to change them to the desired state\. If the rule is not written carefully, the subsequent change to the ACLs fires the rule again, creating an infinite loop\.  
To prevent this, write the rules so that the triggered actions do not re\-fire the same rule\. For example, your rule could fire only if ACLs are found to be in a bad state, instead of after any change\.   
An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified limit\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

You can create an [event pattern](eb-event-patterns.md) that matches a field in an [event](eb-events.md) that has a null value or is an empty string\. Consider the following example event\.

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

To match events where the value of `eventVersion` is an empty string, use the following event pattern, which matches the preceding event\.

```
{
  "detail": {
     "eventVersion": [""]
  }
}
```

To match events where the value of `responseElements` is null, use the following event pattern, which matches the preceding event\.

```
{
  "detail": {
     "responseElements": [null]
  }
}
```

**Note**  
Null values and empty strings are not interchangeable in pattern matching\. An event pattern that matches empty strings doesn't match values of `null`\.