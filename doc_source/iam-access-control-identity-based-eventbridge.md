# Overview of Managing Access Permissions to Your EventBridge Resources<a name="iam-access-control-identity-based-eventbridge"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator IAM user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [EventBridge Resources and Operations](#eventbridge-arn-format)
+ [Understanding Resource Ownership](#understanding-resource-ownership-eventbridge)
+ [Managing Access to Resources](#managing-access-resources-eventbridge)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#actions-effects-principals-eventbridge)
+ [Specifying Conditions in a Policy](#policy-conditions-eventbridge)

## EventBridge Resources and Operations<a name="eventbridge-arn-format"></a>

In EventBridge, the primary resource is a rule\. EventBridge supports other resources that can be used with the primary resource, such as events\. These are referred to as subresources\. These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them\. For more information about ARNs, see [Amazon Resource Names \(ARN\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *Amazon Web Services General Reference*\.


| Resource Type | ARN Format | 
| --- | --- | 
|  Rule  |  `arn:aws:events:region:account:rule/[event-bus-name]/rule-name`  | 
|  Event bus  |  `arn:aws:events:region:account:event-bus/event-bus-name`  | 
|  All EventBridge resources  |  `arn:aws:events:*`  | 
|  All EventBridge resources owned by the specified account in the specified Region  |  `arn:aws:events:region:account:*`  | 

**Note**  
Most services in AWS treat a colon \(`:`\) or a forward slash \(`/`\) as the same character in ARNs\. However, EventBridge uses an exact match in event patterns and rules\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the event that you want to match\.

For example, you can indicate a specific rule \(*myRule*\) in your statement using its ARN as follows\.

```
"Resource": "arn:aws:events:us-east-1:123456789012:rule/myRule"
```

You can also specify all rules that belong to a specific account by using the asterisk \(\*\) wildcard as follows\.

```
"Resource": "arn:aws:events:us-east-1:123456789012:rule/*"
```

To specify all resources or if a specific API action doesn't support ARNs, use the asterisk \(\*\) wildcard in the `Resource` element as follows\.

```
"Resource": "*"
```

Some EventBridge API actions accept multiple resources \(that is, `PutTargets`\)\. To specify multiple resources in a single statement, separate their ARNs with commas, as follows\.

```
"Resource": ["arn1", "arn2"]
```

EventBridge provides a set of operations to work with the EventBridge resources\. For a list of available operations, see [EventBridge Permissions Reference](permissions-reference-eventbridge.md)\.

## Understanding Resource Ownership<a name="understanding-resource-ownership-eventbridge"></a>

The account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the account root user, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root user credentials of your account to create a rule, your account is the owner of the EventBridge resource\.
+ If you create an IAM user in your account and grant permissions to create EventBridge resources to that user, the user can create EventBridge resources\. However, your account, which the user belongs to, owns the EventBridge resources\.
+ If you create an IAM role in your account with permissions to create EventBridge resources, anyone who can assume the role can create EventBridge resources\. Your account, which the role belongs to, owns the EventBridge resources\.

## Managing Access to Resources<a name="managing-access-resources-eventbridge"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of EventBridge\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as identity\-based policies \(IAM policies\) and policies attached to a resource are referred to as resource\-based policies\. EventBridge supports both identity\-based \(IAM policies\) and resource\-based policies\.

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#identity-based-policies-eventbridge)
+ [Resource\-Based Policies \(IAM Policies\)](#resource-based-policies-overview-eventbridge)

### Identity\-Based Policies \(IAM Policies\)<a name="identity-based-policies-eventbridge"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to view rules in the Amazon CloudWatch console, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in account A can create a role to grant cross\-account permissions to another account \(for example, account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in account A\.

  1. Account A administrator attaches a trust policy to the role identifying account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in account B\. Doing this allows users in account B to create or access resources in account A\. The principal in the trust policy can also be an AWS service principal to grant to an AWS service the permissions needed to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

You can create specific IAM policies to restrict the calls and resources that users in your account have access to, and then attach those policies to IAM users\. For more information about how to create IAM roles and to explore example IAM policy statements for EventBridge, see [Overview of Managing Access Permissions to Your EventBridge Resources](#iam-access-control-identity-based-eventbridge)\. 

### Resource\-Based Policies \(IAM Policies\)<a name="resource-based-policies-overview-eventbridge"></a>

When a rule is triggered in EventBridge, all the targets associated with the rule are invoked\. *Invocation* means invoking the AWS Lambda functions, publishing to the Amazon SNS topics, and relaying the event to the Amazon Kinesis streams\. To be able to make API calls against the resources that you own, EventBridge needs the appropriate permissions\. For Lambda, Amazon SNS, and Amazon SQS resources, EventBridge relies on resource\-based policies\. For Kinesis streams, EventBridge relies on IAM roles\.

For more information about how to create IAM roles and to explore example resource\-based policy statements for EventBridge, see [Using Resource\-Based Policies for EventBridge](resource-based-policies-eventbridge.md)\.

## Specifying Policy Elements: Actions, Effects, and Principals<a name="actions-effects-principals-eventbridge"></a>

For each EventBridge resource, the service defines a set of API operations\. To grant permissions for these API operations, EventBridge defines a set of actions that you can specify in a policy\. Some API operations can require permissions for more than one action to perform the API operation\. For more information about resources and API operations, see [EventBridge Resources and Operations](#eventbridge-arn-format) and [EventBridge Permissions Reference](permissions-reference-eventbridge.md)\.

The following are the basic policy elements:
+ **Resource** – You use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. For more information, see [EventBridge Resources and Operations](#eventbridge-arn-format)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, the `events:Describe` permission allows the user permissions to perform the `Describe` operation\.
+ **Effect** – You specify the effect, either allow or deny, when the user requests the specific action\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user can't access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\.

To learn more about IAM policy syntax and descriptions, see [IAM JSON Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the EventBridge API actions and the resources that they apply to, see [EventBridge Permissions Reference](permissions-reference-eventbridge.md)\.

## Specifying Conditions in a Policy<a name="policy-conditions-eventbridge"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

To express conditions, you use predefined condition keys\. There are AWS\-wide condition keys and EventBridge–specific keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. For a complete list of EventBridge–specific keys, see [Using IAM Policy Conditions for Fine\-Grained Access Control](policy-keys-eventbridge.md)\.