# Arrays in EventBridge Event Patterns<a name="arrays-in-eventbridge-event-patterns"></a>

The value of each field in a pattern is an array containing one or more values, and the pattern matches if any of the values in the array match the value in the event\. If the value in the event is an array, then the pattern matches if the intersection of the pattern array and the event array is non\-empty\.

For example, consider an event pattern that includes the following\.

```
"resources": [
   "arn:aws:ec2:us-east-1:123456789012:instance/i-b188560f",
   "arn:aws:ec2:us-east-1:111122223333:instance/i-b188560f",
   "arn:aws:ec2:us-east-1:444455556666:instance/i-b188560f",
]
```

 This example pattern matches an event that includes the following text, because the first item in the pattern array matches the second item in the event array\.

```
"resources": [
   "arn:aws:autoscaling:us-east-1:123456789012:autoScalingGroup:eb56d16b-bbf0-401d-b893-d5978ed4a025:autoScalingGroupName/ASGTerminate",
   "arn:aws:ec2:us-east-1:123456789012:instance/i-b188560f" 
]
```