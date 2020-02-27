# Tag\-based Policies<a name="tag-based-policies"></a>

Amazon EventBridge supports policies based on tags\. For example, you could restrict access to resources that include a tag with the key `environment` and the value `production`\.

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
                "StringEquals": {"aws:ResourceTag/environment": "production"}
            }
        }
    ]
}
```

This policy will `Deny` the ability to create, delete, or modify tags, rules, or event buses for resources that have been tagged `environment/production`\.

For more information about tagging, see the following\.
+ [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)
+ [Controlling Access Using IAM Tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_iam-tags.html)