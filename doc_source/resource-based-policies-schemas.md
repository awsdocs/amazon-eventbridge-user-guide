# Using Resource\-Based Policies with the EventBridge Schema Registry<a name="resource-based-policies-schemas"></a>

The EventBridge Schema Registry supports resource\-based policies\. A *resource policy* is a policy that is attached to a resource rather than to an IAM identity\. For example, in Amazon Simple Storage Service \(Amazon S3\), a resource policy is attached to an Amazon S3 bucket\. 

For more information on EventBridge Schemas and resource\-based policies, see the following\.
+ [Amazon EventBridge Schemas REST API Reference](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/what-is-eventbridge-schemas.html)
+ [Identity\-Based Policies and Resource\-Based Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) in the IAM User Guide

## Supported APIs for Resource\-based Policies<a name="resource-supported-apis"></a>

The following APIs can be used with resource\-based policies for the EventBridge Schema Registry\.
+ `DescribeRegistry`
+ `UpdateRegistry`
+ `DeleteRegistry`
+ `ListSchemas`
+ `SearchSchemas`
+ `DescribeSchema`
+ `CreateSchema`
+ `DeleteSchema`
+ `UpdateSchema`
+ `ListSchemaVersions`
+ `DeleteSchemaVersion`
+ `DescribeCodeBinding`
+ `GetCodeBindingSource`
+ `PutCodeBinding`

## Example Policy Granting All Supported actions to an AWS Account<a name="resource-policy-account-all"></a>

For the EventBridge Schema Registry, a resource\-based policy is always attached to a registry\. To enable access to a schema, you must specify the schema ARN as well as the registry ARN in the policy\.

To grant a user access to all available APIs for EventBridge Schemas, use a policy similar to the following, where the `"Principal"` is the account ID of the account you want to grant access\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Test",
            "Effect": "Allow",
            "Action": [
                "schemas:*"
            ],
            "Principal": {
                "AWS": [
                    "109876543210"
                ]
            },
            "Resource": [
                "arn:aws:schemas:us-east-1:012345678901:registry/default",
                "arn:aws:schemas:us-east-1:012345678901:schema/default*"
            ]
        }
    ]
}
```

## Example Policy Granting Read\-only Actions to an AWS Account<a name="resource-policy-account-read"></a>

The following example grants access to an account for only the read\-only APIs for EventBridge Schemas\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Test",
            "Effect": "Allow",
            "Action": [
                "schemas:DescribeRegistry",
                "schemas:ListSchemas",
                "schemas:SearchSchemas",
                "schemas:DescribeSchema",
                "schemas:ListSchemaVersions",
                "schemas:DescribeCodeBinding",
                "schemas:GetCodeBindingSource"
            ],
            "Principal": {
                "AWS": [
                    "109876543210"
                ]
            },
            "Resource": [
                "arn:aws:schemas:us-east-1:012345678901:registry/default",
                "arn:aws:schemas:us-east-1:012345678901:schema/default*"
            ]
        }
    ]
}
```

## Example Policy Granting All Actions to an Organization<a name="resource-policy-org"></a>

You can also use resource\-based policies with the EventBridge Schema Registry to grant access to an organization ID\. For more information, see the [AWS Organizations User Guide](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Test",
            "Effect": "Allow",
            "Action": [
                "schemas:*"
            ],
            "Principal": "*",
            "Resource": [
                "arn:aws:schemas:us-east-1:012345678901:registry/default",
                "arn:aws:schemas:us-east-1:012345678901:schema/default*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalOrgID": [
                        "o-a1b2c3d4e5"
                    ]
                }
            }
        }
    ]
}
```