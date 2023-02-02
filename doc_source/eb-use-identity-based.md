# Using identity\-based policies \(IAM policies\) for Amazon EventBridge<a name="eb-use-identity-based"></a>

Identity\-based policies are permissions policies that you attach to IAM identities \.

**Topics**
+ [AWS managed policies for EventBridge](#eb-managed-policies)
+ [Permissions required for EventBridge to access targets using IAM roles](#eb-target-permissions)
+ [Customer\-managed policy example: Using tagging to control access to rules](#eb-customer-managed-policies)
+ [Amazon EventBridge updates to AWS managed policies](#eb-use-identity-based-awsmanpol-updates)

## AWS managed policies for EventBridge<a name="eb-managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. *Managed*, or predefined, policies grant the necessary permissions for common use cases, so you don't need to investigate what permissions are needed\. For more information, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies that you can attach to users in your account are specific to EventBridge:
+ [**AmazonEventBridgeFullAccess**](#eb-full-access-policy) – Grants full access to EventBridge, including EventBridge Pipes, EventBridge Schemas and EventBridge Scheduler\.
+ [**AmazonEventBridgeReadOnlyAccess**](#eb-read-only-access-policy) – Grants read\-only access to EventBridge, including EventBridge Pipes, EventBridge Schemas and EventBridge Scheduler\.

### AmazonEventBridgeFullAccess policy<a name="eb-full-access-policy"></a>

The AmazonEventBridgeFullAccess policy grants permissions to use all EventBridge actions, as well as the following permissions:
+ `iam:CreateServiceLinkedRole` – EventBridge requires this permission to create the service role in your account for API destinations\. This permission grants only the IAM service permissions to create a role in your account specifically for API destinations\.
+ `iam:PassRole` – EventBridge requires this permission to pass an invocation role to EventBridge to invoke the target of a rule\.
+ **Secrets Manager permissions** – EventBridge requires these permissions to manage secrets in your account when you provide credentials through the connection resource to authorize API Destinations\.

The following JSON shows the AmazonEventBridgeFullAccess policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EventBridgeActions",        
            "Effect": "Allow",
            "Action": [
                "events:*",
                "schemas:*",
                "scheduler:*",
                "pipes:*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "IAMCreateServiceLinkedRoleForApiDestinations",        
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/AmazonEventBridgeApiDestinationsServiceRolePolicy",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": "apidestinations.events.amazonaws.com"
                }
            }
        },
        {
            "Sid": "IAMCreateServiceLinkedRoleForAmazonEventBridgeSchemas",
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "arn:aws:iam::*:role/aws-service-role/schemas.amazonaws.com/AWSServiceRoleForSchemas",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": "schemas.amazonaws.com"
                }
            }            
        },
        {
            "Sid": "SecretsManagerAccessForApiDestinations",        
            "Effect": "Allow",
            "Action": [
                "secretsmanager:CreateSecret",
                "secretsmanager:UpdateSecret",
                "secretsmanager:DeleteSecret",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:events!*"
        },
        {
            "Sid": "IAMPassRoleAccessForEventBridge",                
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/*",
            "Condition": {
                "StringLike": {
                    "iam:PassedToService": "events.amazonaws.com"
                }
            }
        },
        {
            "Sid": "IAMPassRoleAccessForScheduler",        
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/*",
            "Condition": {
                "StringLike": {
                    "iam:PassedToService": "scheduler.amazonaws.com"
                }
            }
        },
        {
            "Sid": "IAMPassRoleAccessForPipes",        
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/*",
            "Condition": {
                "StringLike": {
                    "iam:PassedToService": "pipes.amazonaws.com"
                }
            }
        }
    ]
}
```

**Note**  
The information in this section also applies to the `CloudWatchEventsFullAccess` policy\. However, it is strongly recommended that you use Amazon EventBridge instead of Amazon CloudWatch Events\.

### AmazonEventBridgeReadOnlyAccess policy<a name="eb-read-only-access-policy"></a>

The AmazonEventBridgeReadOnlyAccess policy grants permissions to use all read EventBridge actions\.

The following JSON shows the AmazonEventBridgeReadOnlyAccess policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "events:DescribeRule",
                "events:DescribeEventBus",
                "events:DescribeEventSource",
                "events:ListEventBuses",
                "events:ListEventSources",
                "events:ListRuleNamesByTarget",
                "events:ListRules",
                "events:ListTargetsByRule",
                "events:TestEventPattern",
                "events:DescribeArchive",
                "events:ListArchives",
                "events:DescribeReplay",
                "events:ListReplays",
                "events:DescribeConnection",
                "events:ListConnections",
                "events:DescribeApiDestination",
                "events:ListApiDestinations",
                "events:DescribeEndpoint",
                "events:ListEndpoints",
                "schemas:DescribeCodeBinding",
                "schemas:DescribeDiscoverer",
                "schemas:DescribeRegistry",
                "schemas:DescribeSchema",
                "schemas:ExportSchema",
                "schemas:GetCodeBindingSource",
                "schemas:GetDiscoveredSchema",
                "schemas:GetResourcePolicy",
                "schemas:ListDiscoverers",
                "schemas:ListRegistries",                
                "schemas:ListSchemas",                                
                "schemas:ListSchemaVersions",                                                
                "schemas:ListTagsForResource",
                "schemas:SearchSchemas",
                "scheduler:GetSchedule",
                "scheduler:GetScheduleGroup",                
                "scheduler:ListSchedules",
                "scheduler:ListScheduleGroups",                
                "scheduler:ListTagsForResource",
                "pipes:DescribePipe",
                "pipes:ListPipes",
                "pipes:ListTagsForResource"                
            ],
            "Resource": "*"
        }
    ]
}
```

**Note**  
The information in this section also applies to the `CloudWatchEventsReadOnlyAccess` policy\. However, it is strongly recommended that you use Amazon EventBridge instead of Amazon CloudWatch Events\.

### EventBridge Schema\-specific managed policies<a name="eb-schemas-access-policies"></a>

[A schema](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-schema.html) defines the structure of events that are sent to EventBridge\. EventBridge provides schemas for all events that are generated by AWS services\. The following AWS managed policies specific to EventBridge Schemas are available:
+ [AmazonEventBridgeSchemasServiceRolePolicy](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/policies/arn:aws:iam::aws:policy/aws-service-role/AmazonEventBridgeSchemasServiceRolePolicy$jsonEditor)
+ [AmazonEventBridgeSchemasFullAccess](https://us-east-1.console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEventBridgeSchemasFullAccess$jsonEditor)
+ [AmazonEventBridgeSchemasReadOnlyAccess](https://us-east-1.console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEventBridgeSchemasReadOnlyAccess$jsonEditor)

### EventBridge Scheduler\-specific managed policies<a name="eb-scheduler-access-policies"></a>

Amazon EventBridge Scheduler is a serverless scheduler that allows you to create, run, and manage tasks from one central, managed service\. For AWS managed policies that are specific to EventBridge Scheduler, see [AWS managed policies for EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/security_iam_id-based-policies.html#security_iam_id-based-policies-managed-policies) in the *EventBridge Scheduler User Guide*\.

### EventBridge Pipes\-specific managed policies<a name="eb-pipes-access-policies"></a>

Amazon EventBridge Pipes connects event sources to targets\. Pipes reduces the need for specialized knowledge and integration code when developing event driven architectures\. This helps ensures consistency across your company’s applications\. The following AWS managed policies specific to EventBridge Pipes are available:
+ [AmazonEventBridgePipesFullAccess](https://us-east-1.console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEventBridgePipesFullAccess$jsonEditor)

  Provides full access to Amazon EventBridge Pipes\.
**Note**  
This policy provides `iam:PassRole` – EventBridge Pipes requires this permission to pass an invocation role to EventBridge to create, and start pipes\.
+ [AmazonEventBridgePipesReadOnlyAccess](https://us-east-1.console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEventBridgePipesReadOnlyAccess$jsonEditor)

  Provides read\-only access to Amazon EventBridge Pipes\.
+ [AmazonEventBridgePipesOperatorAccess](https://us-east-1.console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AmazonEventBridgePipesOperatorAccess$jsonEditor)

  Provides read\-only and operator \(that is, the ability to stop and start running Pipes\) access to Amazon EventBridge Pipes\.

### IAM roles for sending events<a name="eb-events-iam-roles"></a>

To relay events to targets, EventBridge needs an IAM role\.

**To create an IAM role for sending events to EventBridge**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. To create an IAM role, follow the steps in [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide* \. As you follow the steps, do the following:
   + In **Role Name**, use a name that is unique within your account\.
   + In **Select Role Type**, choose **AWS Service Roles**, and then choose **Amazon EventBridge**\. This grants EventBridge permissions to assume the role\.
   + In **Attach Policy**, choose **AmazonEventBridgeFullAccess**\.

You can also create your own custom IAM policies to allow permissions for EventBridge actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\. For more information about IAM policies, see [Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html) in the *IAM User Guide*\.

## Permissions required for EventBridge to access targets using IAM roles<a name="eb-target-permissions"></a>

EventBridge targets typically require IAM roles that grant permission to EventBridge to invoke the target\. The following are some examples for various AWS services and targets\. For others, use the EventBridge console to create a Rule and create a new Role which will be created with a policy with well\-scoped permissions preconfigured\. 

Amazon SQS, Amazon SNS, Lambda, CloudWatch Logs, and EventBridge bus targets do not use roles, and permissions to EventBridge must be granted via a resource policy\. API Gateway targets can use either resource policies or IAM roles\.

If the target is an API destination, the role that you specify must include the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
             "Action": [ "events:InvokeApiDestination" ],
            "Resource": [ "arn:aws:events:::api-destination/*" ]
        }
     ]
}
```

If the target is a Kinesis stream, the role used to send event data to that target must include the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecord"
            ],
            "Resource": "*"
        }
    ]
}
```

If the target is Systems Manager run command, and you specify one or more `InstanceIds` values for the command, the role that you specify must include the following policy\.

```
{
     "Version": "2012-10-17",
     "Statement": [
           {
                "Action": "ssm:SendCommand",
                "Effect": "Allow",
                "Resource": [
                     "arn:aws:ec2:region:accountId:instance/instanceIds",
                     "arn:aws:ssm:region:*:document/documentName"
                ]
           }
     ]
}
```

If the target is Systems Manager run command, and you specify one or more tags for the command, the role that you specify must include the following policy\.

```
{
     "Version": "2012-10-17",
     "Statement": [
           {
                "Action": "ssm:SendCommand",
                "Effect": "Allow",
                "Resource": [
                     "arn:aws:ec2:region:accountId:instance/*"
                ],
                "Condition": {
                    "StringEquals": {
                        "ec2:ResourceTag/*": [
                            "[[tagValues]]"
                        ]
                    }
                }
           },
           {
                "Action": "ssm:SendCommand",
                "Effect": "Allow",
                "Resource": [
                     "arn:aws:ssm:region:*:document/documentName"
                ]
           }
     ]
}
```

If the target is an AWS Step Functions state machine, the role that you specify must include the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
             "Action": [ "states:StartExecution" ],
            "Resource": [ "arn:aws:states:*:*:stateMachine:*" ]
        }
     ]
}
```

If the target is an Amazon ECS task, the role that you specify must include the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
            "ecs:RunTask"
        ],
        "Resource": [
            "arn:aws:ecs:*:account-id:task-definition/task-definition-name"
        ],
        "Condition": {
            "ArnLike": {
                "ecs:cluster": "arn:aws:ecs:*:account-id:cluster/cluster-name"
            }
        }
    },
    {
        "Effect": "Allow",
        "Action":"iam:PassRole",
        "Resource": [
            "*"
        ],
        "Condition": {
            "StringLike": {
                "iam:PassedToService": "ecs-tasks.amazonaws.com"
            }
        }
    }]
}
```

The following policy allows built\-in targets in EventBridge to perform Amazon EC2 actions on your behalf\. You need to use the AWS Management Console to create rules with built\-in targets\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "TargetInvocationAccess",
            "Effect": "Allow",
            "Action": [
                "ec2:Describe*",
                "ec2:RebootInstances",
                "ec2:StopInstances",
                "ec2:TerminateInstances",
                "ec2:CreateSnapshot"
            ],
            "Resource": "*"
        }
    ]
}
```

The following policy allows EventBridge to relay events to the Kinesis streams in your account\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "KinesisAccess",
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecord"
            ],
            "Resource": "*"
        }
    ]
}
```

## Customer\-managed policy example: Using tagging to control access to rules<a name="eb-customer-managed-policies"></a>

The following example shows a user policy that grant permissions for EventBridge actions\. This policy works when you use the EventBridge API, AWS SDKs, or the AWS CLI\.

You can grant users access to specific EventBridge rules while preventing them from accessing other rules\. To do so, you tag both sets of rules and then use IAM policies that refer to those tags\. For more information about tagging EventBridge resources, see [Amazon EventBridge tags](eb-tagging.md)\.

You can grant an IAM policy to a user to allow access to only the rules with a particular tag\. You choose which rules to grant access to by tagging them with that particular tag\. For example, the following policy grants a user access to rules with the value of `Prod` for the tag key `Stack`\.

```
{
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "events:*",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/Stack": "Prod"
                }
            }
        }
    ]
}
```

For more information about using IAM policy statements, see [Controlling Access Using Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html) in the *IAM User Guide*\.

## Amazon EventBridge updates to AWS managed policies<a name="eb-use-identity-based-awsmanpol-updates"></a>



View details about updates to AWS managed policies for EventBridge since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the EventBridge Document history page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  [AmazonEventBridgePipesFullAccess](#eb-pipes-access-policies) – New policy added  |  EventBridge added managed policy for full permissions for using EventBridge Pipes\.  | December 1, 2022 | 
|  [AmazonEventBridgePipesReadOnlyAccess](#eb-pipes-access-policies) – New policy added  |  EventBridge added managed policy for permissions to view EventBridge Pipes information resources\.  | December 1, 2022 | 
|  [AmazonEventBridgePipesOperatorAccess](#eb-pipes-access-policies) – New policy added  |  EventBridge added managed policy for for permissions to view EventBridge Pipes information, as well as start and stop running pipes\.  | December 1, 2022 | 
|  [AmazonEventBridgeFullAccess](#eb-full-access-policy) – Update to an existing policy  |  EventBridge updated the policy to include permissions necessary for using EventBridge Pipes features\.  | December 1, 2022 | 
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view EventBridge Pipes information resources\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | December 1, 2022 | 
|  [CloudWatchEventsReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  Updated to match AmazonEventBridgeReadOnlyAccess\.  | December 1, 2022 | 
|  [CloudWatchEventsFullAccess](#eb-full-access-policy) – Update to an existing policy  |  Updated to match AmazonEventBridgeFullAccess\.  | December 1, 2022 | 
|  [AmazonEventBridgeFullAccess](#eb-full-access-policy) – Update to an existing policy  |  EventBridge updated the policy to include permissions necessary for using schemas and scheduler features\. The following permissions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | November 10, 2022 | 
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view schema and scheduler information resources\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | November 10, 2022 | 
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view endpoint information\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | April 7, 2022 | 
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view connection and API destination information\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | March 4, 2021 | 
|  [AmazonEventBridgeFullAccess](#eb-full-access-policy) – Update to an existing policy  |  EventBridge updated the policy to include `iam:CreateServiceLinkedRole` and AWS Secrets Manager permissions necessary for using API destinations\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | March 4, 2021 | 
|  EventBridge started tracking changes  |  EventBridge started tracking changes for its AWS managed policies\.  | March 4, 2021 | 