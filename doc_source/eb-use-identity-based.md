# Using identity\-based policies \(IAM policies\) for Amazon EventBridge<a name="eb-use-identity-based"></a>

Identity\-based policies are permissions policies that you attach to IAM identities \.

**Topics**
+ [Permissions required to use EventBridge](#eb-console-permissions)
+ [AWS managed policies for EventBridge](#eb-managed-policies)
+ [Permissions required for EventBridge to access targets using IAM roles](#eb-target-permissions)
+ [Customer\-managed policy examples](#eb-customer-managed-policies)
+ [Amazon EventBridge updates to AWS managed policies](#eb-use-identity-based-awsmanpol-updates)

## Permissions required to use EventBridge<a name="eb-console-permissions"></a>

For a user to work with the EventBridge console or API, that user must have a minimum set of permissions to access other AWS resources\. Other AWS services can send [events](eb-events.md) to EventBridge or be a [target](eb-targets.md) of an EventBridge [rule](eb-rules.md)\. The following list shows examples of AWS services and their corresponding minimum permissions:
+ Automation
  + `automation:CreateAction`
  + `automation:DescribeAction`
  + `automation:UpdateAction`
+ Amazon EC2 Auto Scaling
  + `autoscaling:DescribeAutoScalingGroups`
+ AWS CloudTrail
  + `cloudtrail:DescribeTrails`
+ Amazon EC2
  + `ec2:DescribeInstances`
  + `ec2:DescribeVolumes`
+ EventBridge
  + `events:DeleteRule` 
  + `events:DescribeRule` 
  + `events:DisableRule` 
  + `events:EnableRule` 
  + `events:ListRuleNamesByTarget` 
  + `events:ListRules` 
  + `events:ListTargetsByRule` 
  + `events:PutEvents`
  + `events:PutRule`
  + `events:PutTargets` 
  + `events:RemoveTargets` 
  + `events:TestEventPattern` 
+ IAM
  + `iam:ListRoles`
+ Kinesis
  + `kinesis:ListStreams`
+ Lambda
  + `lambda:AddPermission`
  + `lambda:ListFunctions`
  + `lambda:RemovePermission`
+ Amazon SNS
  + `sns:GetTopicAttributes`
  + `sns:ListTopics`
  + `sns:SetTopicAttributes`
+ Amazon SWF
  + `swf:DescribeAction`
  + `swf:ReferenceAction`
  + `swf:RegisterAction`
  + `swf:RegisterDomain`
  + `swf:UpdateAction`

If you create an IAM policy that is more restrictive than the minimum required permissions, the EventBridge console won't function as intended for users with that IAM policy\. To ensure that those users can still use the EventBridge console, also attach the `AmazonEventBridgeReadOnlyAccess` managed policy to the user, as described in [AWS managed policies for EventBridge](#eb-managed-policies)\.

You don't need to allow minimum permissions for users that are making calls only to the AWS CLI\.

## AWS managed policies for EventBridge<a name="eb-managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. *Managed*, or predefined, policies grant the necessary permissions for common use cases, so you don't need to investigate what permissions are needed\. For more information, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies that you can attach to users in your account are specific to EventBridge:
+ [**AmazonEventBridgeFullAccess**](#eb-full-access-policy) – Grants full access to EventBridge\.
+ [**AmazonEventBridgeReadOnlyAccess**](#eb-read-only-access-policy) – Grants read\-only access to EventBridge\.

### AmazonEventBridgeFullAccess policy<a name="eb-full-access-policy"></a>

The AmazonEventBridgeFullAccess policy grants permissions to use all EventBridge actions, as well as the following permissions:
+ `iam:CreateServiceLinkedRole` – EventBridge requires this permission to create the service role in your account for API destinations\. This permission grants only the IAM service permissions to create a role in your account specifically for API destinations\.
+ `iam:PassRole` – EventBridge requires this permission to pass an invocation role to EventBridge to invoke the target of a rule\.
+ **Secrets Manager permissions** – EventBridge requires these permissions to manage secrets in your account when you provide credentials through the connection resource to authorize API Destinations\.

The following JSON shows the AmazonEventBridgeFullAccess policy\.

```
{
    	"Version": "2012-10-17",
    	"Statement": [{
    			"Effect": "Allow",
    			"Action": "events:*",
    			"Resource": "*"
    		}, {
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
    			"Effect": "Allow",
    			"Action": "iam:PassRole",
    			"Resource": "arn:aws:iam::*:role/*",
    			"Condition": {
    				"StringLike": {
    					"iam:PassedToService": "events.amazonaws.com"
    				}
    			}
    		}
    	]
    }
```

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
				"events:ListEndpoints",
				"events:DescribeEndpoint"
			],
			"Resource": "*"
		}
	]
}
```

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

For EventBridge to access targets that are an API destination, a Kinesis stream, a Systems Manager Run Command, an AWS Step Functions state machine, or an Amazon Elastic Container Service task, you must specify an IAM role for accessing that target, and the role must have a certain policy attached\.

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

## Customer\-managed policy examples<a name="eb-customer-managed-policies"></a>

The following examples show user policies that grant permissions for EventBridge actions\. These policies work when you use the EventBridge API, AWS SDKs, or the AWS CLI\.

**Note**  
All examples use the US West \(Oregon\) Region \(us\-west\-2\) and contain fictitious account IDs that you need to replace to use these policies\. 

You can use the following sample IAM policies listed to limit the EventBridge access for your IAM users and roles\.

### Example 1: Access to Amazon EC2 targets<a name="eb-example-policy-builtin-target"></a>

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

### Example 2: Kinesis<a name="eb-example-policy-invocation-access"></a>

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

### Example 3: Console access<a name="eb-example-policy-console-access"></a>

The following policy allows IAM users to use the EventBridge console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ConsoleAccess",
            "Effect": "Allow",
            "Action": [
                "automation:CreateAction",
                "automation:DescribeAction",
                "automation:UpdateAction",
                "autoscaling:DescribeAutoScalingGroups",
                "cloudtrail:DescribeTrails",
                "ec2:DescribeInstances",
                "ec2:DescribeVolumes",
                "events:*",
                "iam:ListRoles",
                "kinesis:ListStreams",
                "lambda:AddPermission",
                "lambda:ListFunctions",
                "lambda:RemovePermission",
                "sns:GetTopicAttributes",
                "sns:ListTopics",
                "sns:SetTopicAttributes",
                "swf:DescribeAction",
                "swf:ReferenceAction",
                "swf:RegisterAction",
                "swf:RegisterDomain",
                "swf:UpdateAction"
            ],
            "Resource": "*"
        },
        {
            "Sid": "IAMPassRole",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": [
                "arn:aws:iam::*:role/AWS_Events_Invoke_Targets",
                "arn:aws:iam::*:role/AWS_Events_Actions_Execution"
            ]
        }
    ]
}
```

### Example 4: EventBridgeFullAccess<a name="eb-example-policy-full-access"></a>

The following policy allows all AWS resources to perform actions against EventBridge through the AWS CLI and the SDK\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "FullAccess",
            "Effect": "Allow",
            "Action": "events:*",
            "Resource": "*"
        },
        {
            "Sid": "IAMPassRole",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/AWS_Events_Invoke_Targets"
        }
    ]
}
```

### Example 5: ReadOnlyAccess<a name="eb-example-policy-readonly-access"></a>

The following policy allows all AWS resources to have read\-only access to EventBridge\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadOnlyAccess",
            "Effect": "Allow",
            "Action": [
                "events:Describe*",
                "events:List*",
                "events:TestEventPattern"
            ],
            "Resource": "*"
        }
    ]
}
```

### Example 6: Using tagging to control access to rules<a name="eb-cwl-iam-policy-tagging"></a>

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
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view endpoint information\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | April 7, 2022 | 
|  [AmazonEventBridgeReadOnlyAccess](#eb-read-only-access-policy) – Update to an existing policy  |  EventBridge added permissions necessary for view connection and API destination information\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | March 4, 2021 | 
|  [AmazonEventBridgeFullAccess](#eb-full-access-policy) – Update to an existing policy  |  EventBridge updated the policy to include `iam:CreateServiceLinkedRole` and AWS Secrets Manager permissions necessary for using API destinations\. The following actions were added: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html)  | March 4, 2021 | 
|  EventBridge started tracking changes  |  EventBridge started tracking changes for its AWS managed policies\.  | March 4, 2021 | 
