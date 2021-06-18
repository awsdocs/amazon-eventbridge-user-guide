# Resource\-based policies for Amazon EventBridge schemas<a name="eb-resource-based-schemas"></a>

The EventBridge [schema registry](eb-schema-registry.md) supports [resource\-based policies](eb-use-resource-based.md)\. A *resource\-based policy* is a policy that is attached to a resource rather than to an IAM identity\. For example, in Amazon Simple Storage Service \(Amazon S3\), a resource policy is attached to an Amazon S3 bucket\. 

For more information about EventBridge Schemas and resource\-based policies, see the following\.
+ [Amazon EventBridge Schemas REST API Reference](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/what-is-eventbridge-schemas.html)
+ [Identity\-Based Policies and Resource\-Based Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) in the IAM User Guide

## Supported APIs for resource\-based policies<a name="eb-resource-supported-apis"></a>

You can use the following APIs with resource\-based policies for the EventBridge schema registry\.
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

## Example policy granting all supported actions to an AWS account<a name="eb-resource-policy-account-all"></a>

For the EventBridge schema registry, you must always attach a resource\-based policy to a registry\. To grant access to a schema, you specify the schema ARN and the registry ARN in the policy\.

To grant a user access to all available APIs for EventBridge Schemas, use a policy similar to the following, replacing the `"Principal"` with the account ID of the account you want to grant access\.

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

## Example policy granting read\-only actions to an AWS account<a name="eb-resource-policy-account-read"></a>

The following example grants access to an account for only the read\-only APIs for EventBridge schemas\.

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

## Example policy granting all actions to an organization<a name="eb-resource-policy-org"></a>

You can use resource\-based policies with the EventBridge schema registry to grant access to an organization\. For more information, see the [AWS Organizations User Guide](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)\. The following example grants organization with an ID of `o-a1b2c3d4e5` access to the schema registry\.

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