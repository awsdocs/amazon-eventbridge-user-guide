# Tag\-based policies<a name="eb-tag-policies"></a>

In Amazon EventBridge, you can use policies based on tags to control access to resources\. 

For example, you could restrict access to resources that include a tag with the key `environment` and the value `production`\. The following example policy denies any resource with this tag the ability to create, delete, or modify tags, rules, or event buses for resources that have been tagged `environment/production`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "events:PutRule",
                "events:DescribeRule",
                "events:DeleteRule",
                "events:CreateEventBus",
                "events:DescribeEventBus"
                "events:DeleteEventBus"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {"aws:TagKey/environment": "production"}
            }
        }
    ]
}
```

For more information about tagging, see the following\.
+ [Amazon EventBridge tags](eb-tagging.md)
+ [Controlling Access Using IAM Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_iam-tags.html)