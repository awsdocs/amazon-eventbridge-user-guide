# Arrays in Amazon EventBridge event patterns<a name="eb-event-patterns-arrays"></a>

The value of each field in an [event pattern](eb-event-patterns.md) is an array containing one or more values\. An event pattern matches the [event](eb-events.md) if any of the values in the array match the value in the event\. If the value in the event is an array, then the event pattern matches if the intersection of the event pattern array and the event array is non\-empty\.

**Important**  
In EventBridge, it is possible to create rules that lead to infinite loops, where a rule is fired repeatedly\. For example, a rule might detect that ACLs have changed on an S3 bucket, and trigger software to change them to the desired state\. If the rule is not written carefully, the subsequent change to the ACLs fires the rule again, creating an infinite loop\.  
To prevent this, write the rules so that the triggered actions do not re\-fire the same rule\. For example, your rule could fire only if ACLs are found to be in a bad state, instead of after any change\.   
An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified limit\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

For example, consider an event pattern that includes the following field\.

```
"resources": [
   "arn:aws:ec2:us-east-1:123456789012:instance/i-b188560f",
   "arn:aws:ec2:us-east-1:111122223333:instance/i-b188560f",
   "arn:aws:ec2:us-east-1:444455556666:instance/i-b188560f",
]
```

The preceding event pattern matches an event that includes the following field because the first item in the event pattern array matches the second item in the event array\.

```
"resources": [
   "arn:aws:autoscaling:us-east-1:123456789012:autoScalingGroup:eb56d16b-bbf0-401d-b893-d5978ed4a025:autoScalingGroupName/ASGTerminate",
   "arn:aws:ec2:us-east-1:123456789012:instance/i-b188560f" 
]
```