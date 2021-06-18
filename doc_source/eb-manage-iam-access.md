# Managing access permissions to your Amazon EventBridge resources<a name="eb-manage-iam-access"></a>

You manage access to EventBridge resources such as [rules](eb-rules.md) or [events](eb-events.md) by using [identity\-based](eb-use-identity-based.md) or [resource\-based](eb-use-resource-based.md) policies\.

## EventBridge resources<a name="eb-arn-format"></a>

EventBridge resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them\. You use ARNs in EventBridge to create event patterns\. For more information about ARNs, see [Amazon Resource Names \(ARN\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\.



For a list of operations EventBridge provides for working with resources, see [Amazon EventBridge permissions reference](eb-permissions-reference.md)\.

**Note**  
Most services in AWS treat a colon \(`:`\) or a forward slash \(`/`\) as the same character in ARNs\. However, EventBridge uses an exact match in [event patterns](eb-event-patterns.md) and rules\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the event that you want to match\.

The following table shows the resources in EventBridge\.


| Resource Type | ARN Format | 
| --- | --- | 
|  Archive  |  `arn:aws:events:region:account:archive/archive-name`  | 
|  Replay  |  `arn:aws:events:region:account:replay/replay-name`  | 
|  Rule  |  `arn:aws:events:region:account:rule/[event-bus-name]/rule-name`  | 
|  Event bus  |  `arn:aws:events:region:account:event-bus/event-bus-name`  | 
|  All EventBridge resources  |  `arn:aws:events:*`  | 
|  All EventBridge resources owned by the specified account in the specified Region  |  `arn:aws:events:region:account:*`  | 

The following example shows how to indicate a specific rule \(*myRule*\) in your statement using its ARN\.

```
"Resource": "arn:aws:events:us-east-1:123456789012:rule/myRule"
```

To specify all rules that belong to a specific account by using the asterisk \(\*\) wildcard as follows\.

```
"Resource": "arn:aws:events:us-east-1:123456789012:rule/*"
```

To specify all resources, or if a specific API action doesn't support ARNs, use the asterisk \(\*\) wildcard in the `Resource` element as follows\.

```
"Resource": "*"
```

To specify multiple resources or `PutTargets` in a single statement, separate their ARNs with commas as follows\.

```
"Resource": ["arn1", "arn2"]
```

## Resource ownership<a name="eb-understanding-resource-ownership"></a>

An account owns the resources in the account, no matter who creates the resources\. The resource owner is the account of the *[principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html)*, the account root user, an IAM user, or an IAM role that authenticates the request to create the resource\. The following examples illustrate how this works:
+ If you use the root user credentials of your account to create a rule, your account is the owner of the EventBridge resource\.
+ If you create an IAM user in your account and grant permissions to create EventBridge resources to that user, the user can create EventBridge resources\. However, your account, which the user belongs to, owns the EventBridge resources\.
+ If you create an IAM role in your account with permissions to create EventBridge resources, anyone who can assume the role can create EventBridge resources\. Your account, which the role belongs to, owns the EventBridge resources\.

## Managing access to resources<a name="eb-managing-access-resources"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of EventBridge\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [IAM policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based policies* \(IAM policies\) and policies attached to a resource are referred to as *resource\-based policies*\. In EventBridge, you can use both identity\-based \(IAM policies\) and resource\-based policies\.

**Topics**
+ [Identity\-based policies \(IAM policies\)](#eb-identity-based-policies)
+ [Resource\-based policies \(IAM policies\)](#eb-resource-based-policies-overview)

### Identity\-based policies \(IAM policies\)<a name="eb-identity-based-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permission to view rules in the Amazon CloudWatch console, attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in account A can create a role to grant cross\-account permissions to another account B or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permission on resources in account A\.

  1. Account A administrator attaches a trust policy to the role identifying account B as the principal who can assume the role\.

  1. Account B administrator can then delegate permissions to assume the role to any users in account B\. Doing this allows users in account B to create or access resources in account A\. The principal in the trust policy can also be an AWS service principal to grant to an AWS service the permission needed to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

You can create specific IAM policies to restrict the calls and resources that users in your account have access to and then attach those policies to IAM users\. For more information about how to create IAM roles and to explore example IAM policy statements for EventBridge, see [Managing access permissions to your Amazon EventBridge resources](#eb-manage-iam-access)\. 

### Resource\-based policies \(IAM policies\)<a name="eb-resource-based-policies-overview"></a>

When a rule runs in EventBridge, all of the [targets](eb-targets.md) associated with the rule are invoked, which means invoking the AWS Lambda functions, publishing to the Amazon SNS topics, or relaying the event to the Amazon Kinesis streams\. To make API calls on the resources that you own, EventBridge needs the appropriate permission\. For Lambda, Amazon SNS, and Amazon SQS resources, EventBridge uses resource\-based policies\. For Kinesis streams, EventBridge uses IAM roles\.

For more information about how to create IAM roles and to explore example resource\-based policy statements for EventBridge, see [Using resource\-based policies for Amazon EventBridge](eb-use-resource-based.md)\.

## Specifying policy elements: actions, effects, and principals<a name="eb-actions-effects-principals"></a>

For each EventBridge resource, EventBridge defines a set of API operations\. To grant permissions for these API operations, EventBridge defines a set of actions that you can specify in a policy\. Some API operations require permissions for more than one action to perform the API operation\. For more information about resources and API operations, see [EventBridge resources](#eb-arn-format) and [Amazon EventBridge permissions reference](eb-permissions-reference.md)\.

The following are the basic policy elements:
+ **Resource** – Use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. For more information, see [EventBridge resources](#eb-arn-format)\.
+ **Action** – Use keywords to identify resource operations that you want to allow or deny\. For example, the `events:Describe` permission allows the user to perform the `Describe` operation\.
+ **Effect** – Specify either **allow** or **deny**\. If you don't explicitly grant access to \(allow\) a resource, access is denied\. You can also explicitly deny access to a resource, which you do to make sure that a user can't access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\.

For more information about IAM policy syntax and descriptions, see [IAM JSON Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For information about EventBridge API actions and the resources that they apply to, see [Amazon EventBridge permissions reference](eb-permissions-reference.md)\.

## Specifying conditions in a policy<a name="eb-policy-conditions"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

To define conditions, you use condition keys\. There are AWS condition keys and EventBridge specific keys that you can use as appropriate\. For a complete list of AWS keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. For a complete list of EventBridge specific keys, see [Using IAM policy conditions for fine\-grained access control](eb-use-conditions.md)\.