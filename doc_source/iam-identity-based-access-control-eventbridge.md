# Using Identity\-Based Policies \(IAM Policies\) for EventBridge<a name="iam-identity-based-access-control-eventbridge"></a>

This topic provides examples of identity\-based policies in which an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\.

The following shows an example of a permissions policy that allows a user to put event data into Kinesis\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsInvocationAccess",
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecord"
            ],
            "Resource": "*"
        }
    ]
}
```

The sections in this topic cover the following:

**Topics**
+ [Permissions Required to Use the CloudWatch Console](#console-permissions-eventbridge)
+ [AWS Managed \(Predefined\) Policies for EventBridge](#managed-policies-eventbridge)
+ [Permissions Required for EventBridge to Access Certain Targets](#target-permissions-eventbridge)
+ [Customer Managed Policy Examples](#customer-managed-policies-eventbridge)

## Permissions Required to Use the CloudWatch Console<a name="console-permissions-eventbridge"></a>

For a user to work with EventBridge in the CloudWatch console, that user must have a minimum set of permissions that allow the user to describe other AWS resources for their account\. To use EventBridge in the CloudWatch console, you must have permissions from the following services:
+ Automation
+ Amazon EC2 Auto Scaling
+ AWS CloudTrail
+ CloudWatch
+ EventBridge
+ IAM
+ Kinesis
+ Lambda
+ Amazon SNS
+ Amazon SWF

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the CloudWatch console, also attach the `CloudWatchEventsReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for EventBridge](#managed-policies-eventbridge)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the CloudWatch API\.

The full set of permissions required to work with the CloudWatch console is the following:
+ `automation:CreateAction`
+ `automation:DescribeAction`
+ `automation:UpdateAction`
+ `autoscaling:DescribeAutoScalingGroups`
+ `cloudtrail:DescribeTrails`
+ `ec2:DescribeInstances`
+ `ec2:DescribeVolumes`
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
+ `iam:ListRoles`
+ `kinesis:ListStreams`
+ `lambda:AddPermission`
+ `lambda:ListFunctions`
+ `lambda:RemovePermission`
+ `sns:GetTopicAttributes`
+ `sns:ListTopics`
+ `sns:SetTopicAttributes`
+ `swf:DescribeAction`
+ `swf:ReferenceAction`
+ `swf:RegisterAction`
+ `swf:RegisterDomain`
+ `swf:UpdateAction`

## AWS Managed \(Predefined\) Policies for EventBridge<a name="managed-policies-eventbridge"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to EventBridge:
+ **CloudWatchEventsFullAccess** – Grants full access to EventBridge
+ **CloudWatchEventsInvocationAccess** – Allows EventBridge to relay events to the streams in Amazon Kinesis Data Streams in your account
+ **CloudWatchEventsReadOnlyAccess** – Grants read\-only access to EventBridge
+ **CloudWatchEventsBuiltInTargetExecutionAccess** – Allows built\-in targets in EventBridge to perform Amazon EC2 actions on your behalf

### IAM Roles for Sending Events<a name="events-iam-roles"></a>

In order for EventBridge to relay events to your Kinesis stream targets, you must create an IAM role\.

**To create an IAM role for sending EventBridge**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Follow the steps in [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide* to create an IAM role\. As you follow the steps to create a role, do the following:
   + In **Role Name**, use a name that is unique within your account \(for example, **CloudWatchEventsSending**\)\. 
   + In **Select Role Type**, choose **AWS Service Roles**, and then choose **Amazon EventBridge**\. This grants EventBridge permissions to assume the role\.
   + In **Attach Policy**, choose **CloudWatchEventsInvocationAccess**\.

You can also create your own custom IAM policies to allow permissions for EventBridge actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\. For more information about IAM policies, see [Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html) in the *IAM User Guide*\.

## Permissions Required for EventBridge to Access Certain Targets<a name="target-permissions-eventbridge"></a>

For EventBridge to access certain targets, you must specify an IAM role for accessing that target, and that role must have a certain policy attached\.

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

If the target is Systems Manager Run Command and you're specifying one or more InstanceIds values for the command, the role that you specify must include the following policy\.

```
{
     "Version": "2012-10-17",
     "Statement": [
           {
                "Action": "ssm:SendCommand",
                "Effect": "Allow",
                "Resource": [
                     "arn:aws:ec2:{{region}}:{{accountId}}:instance/[[instanceIds]]",
                     "arn:aws:ssm:{{region}}:*:document/{{documentName}}"
                ]
           }
     ]
}
```

If the target is Systems Manager Run Command and you're specifying one or more tags for the command, the role that you specify must include the following policy\.

```
{
     "Version": "2012-10-17",
     "Statement": [
           {
                "Action": "ssm:SendCommand",
                "Effect": "Allow",
                "Resource": [
                     "arn:aws:ec2:{{region}}:{{accountId}}:instance/*"
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
                     "arn:aws:ssm:{{region}}:*:document/{{documentName}}"
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

If the target is an ECS task, the role that you specify must include the following policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
            "ecs:RunTask"
        ],
        "Resource": [
            "arn:aws:ecs:*:{{account-id}}:task-definition/{{task-definition-name}}"
        ],
        "Condition": {
            "ArnLike": {
                "ecs:cluster": "arn:aws:ecs:*:{{account-id}}:cluster/{{cluster-name}}"
            }
        }
    }]
}
```

## Customer Managed Policy Examples<a name="customer-managed-policies-eventbridge"></a>

In this section, you can find example user policies that grant permissions for various EventBridge actions\. These policies work when you are using the EventBridge API, AWS SDKs, or the AWS CLI\.

**Note**  
All examples use the US West \(Oregon\) Region \(us\-west\-2\) and contain fictitious account IDs\.

You can use the following sample IAM policies listed to limit the EventBridge access for your IAM users and roles\.

**Topics**
+ [Example 1: CloudWatchEventsBuiltInTargetExecutionAccess](#example-policy-eventbridge-builtin-target)
+ [Example 2: CloudWatchEventsInvocationAccess](#example-policy-eventbridge-invocation-access)
+ [Example 3: CloudWatchEventsConsoleAccess](#example-policy-eventbridge-console-access)
+ [Example 4: CloudWatchEventsFullAccess](#example-policy-eventbridge-full-access)
+ [Example 5: CloudWatchEventsReadOnlyAccess](#example-policy-eventbridge-readonly-access)
+ [Example 6: Use Tagging to Control Access to Specific Rules](#cwl-iam-policy-tagging)

### Example 1: CloudWatchEventsBuiltInTargetExecutionAccess<a name="example-policy-eventbridge-builtin-target"></a>

The following policy allows built\-in targets in EventBridge to perform Amazon EC2 actions on your behalf\.

**Important**  
Creating rules with built\-in targets is supported only in the AWS Management Console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsBuiltInTargetExecutionAccess",
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

### Example 2: CloudWatchEventsInvocationAccess<a name="example-policy-eventbridge-invocation-access"></a>

The following policy allows EventBridge to relay events to the streams in Kinesis streams in your account\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsInvocationAccess",
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecord"
            ],
            "Resource": "*"
        }
    ]
}
```

### Example 3: CloudWatchEventsConsoleAccess<a name="example-policy-eventbridge-console-access"></a>

The following policy ensures that IAM users can use the EventBridge console\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsConsoleAccess",
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
            "Sid": "IAMPassRoleForCloudWatchEvents",
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

### Example 4: CloudWatchEventsFullAccess<a name="example-policy-eventbridge-full-access"></a>

The following policy allows performing actions against EventBridge through the AWS CLI and SDK\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsFullAccess",
            "Effect": "Allow",
            "Action": "events:*",
            "Resource": "*"
        },
        {
            "Sid": "IAMPassRoleForCloudWatchEvents",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/AWS_Events_Invoke_Targets"
        }
    ]
}
```

### Example 5: CloudWatchEventsReadOnlyAccess<a name="example-policy-eventbridge-readonly-access"></a>

The following policy provides read\-only access to EventBridge\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CloudWatchEventsReadOnlyAccess",
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

### Example 6: Use Tagging to Control Access to Specific Rules<a name="cwl-iam-policy-tagging"></a>

You can grant users access to specified EventBridge rules while preventing them from accessing other rules\. To do so, tag these rules and use IAM policies that refer to those tags\.

For more information about tagging EventBridge resources, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

When you tag EventBridge rules, you can grant an IAM policy to a user to allow access to only the rules with a particular tag\. For example, the following policy statement grants access to only rules with the value of `Prod` for the tag key `Stack`\.

```
{
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "events:*",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:ResourceTag/Stack": "Prod"
                }
            }
        }
    ]
}
```

For more information about using IAM policy statements, see [Controlling Access Using Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html) in the *IAM User Guide*\.