# Using IAM policy conditions for fine\-grained access control<a name="eb-use-conditions"></a>

To grant permissions, you use the IAM policy language in a policy statement to specify the conditions when a policy should take effect\. For example, you can have a policy that is applied only after a specific date\.

A condition in a policy consists of key\-value pairs\. Condition keys aren't case sensitive\. 

If you specify multiple conditions or keys in a single condition, all conditions and keys must be met for EventBridge to grant permission\. If you specify a single condition with multiple values for one key, EventBridge grants permission if one of the values is met\.

You can use placeholders or *policy variables* when you specify conditions\. For more information, see [Policy Variables](https://docs.aws.amazon.com/IAM/latest/UserGuide/policyvariables.html) in the *IAM User Guide*\. For more information about specifying conditions in an IAM policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_ElementDescriptions.html#Condition) in the *IAM User Guide*\.

By default, IAM users and roles can't access the [events](eb-events.md) in your account\. To access events, a user must be authorized for the `PutRule` API action\. If an IAM user or role is authorized for the `events:PutRule` action, they can create a [rule](eb-rules.md) that matches certain events\. However, for the rule to be useful, the user must also have permissions for the `events:PutTargets` action because, if you want the rule to do more than publish a CloudWatch metric, you must also add a [target](eb-targets.md) to a rule\.

You can provide a condition in the policy statement of an IAM user or role that allows the user or role to create a rule that only matches a specific set of sources and event types\. To grant access to specific sources and types of events, use the `events:source` and `events:detail-type` condition keys\.

Similarly, you can provide a condition in the policy statement of an IAM user or role that allows the user or role to create a rule that only matches a specific resource in your accounts\. To grant access to a specific resource, use the `events:TargetArn` condition key\.

The following example is a policy that allows users to access all events except Amazon EC2 events in EventBridge using a deny statement on the `PutRule` API action\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyPutRuleForAllEC2Events",
            "Effect": "Deny",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": "aws.ec2"
                }
            }
        }
    ]
}
```

The following table shows the condition keys and key and value pairs that you can use in a policy in EventBridge\.


| Condition key | Key value pair | Evaluation types | 
| --- | --- | --- | 
|  aws:SourceAccount  |  The account in which the rule specified by `aws:SourceArn` exists\.  |  Account Id, Null  | 
|  aws:SourceArn  |  The ARN of the rule that is sending the event\.  |  ARN, Null  | 
|  events:creatorAccount  |  `"events:creatorAccount":"creatorAccount"` For *creatorAccount*, use the account ID for the account that created the rule\. Use this condition to authorize API calls on rules from a specific account\.  |  creatorAccount, Null  | 
|  events:detail\-type  |  `"events:detail-type":"detail-type "` Where *detail\-type* is the literal string for the **detail\-type** field of the event such as `"AWS API Call via CloudTrail"` and `"EC2 Instance State-change Notification"`\.   |  Detail Type, Null  | 
|  events: detail\.eventTypeCode  |  `"events:detail.eventTypeCode":"eventTypeCode"` For *eventTypeCode*, use the literal string for the **detail\.eventTypeCode** field of the event, such as `"AWS_ABUSE_DOS_REPORT"`\.  |  eventTypeCode, Null  | 
|  events: detail\.service  |  `"events:detail.service":"service"` For *service*, use the literal string for the **detail\.service** field of the event, such as `"ABUSE"`\.  |  service, Null  | 
|  events: detail\.userIdentity\.principalId  |  `"events:detail.userIdentity.principalId":"principal-id"` For *principal\-id*, use the literal string for the **detail\.userIdentity\.principalId** field of the event with detail\-type `"AWS API Call via CloudTrail"` such as `"AROAIDPPEZS35WEXAMPLE:AssumedRoleSessionName."`\.  |  Principal Id, Null  | 
|  events:eventBusInvocation  |  `"events:eventBusInvocation":"boolean"` For *boolean*, use true when a rule sends an event to a target that is an event bus in another account\. Use false when when a `PutEvents` API call is used\.  |  eventBusInvocation, Null  | 
|  events:ManagedBy  |  Used internally by AWS services\. For a rule created by an AWS service on your behalf, the value is the principal name of the service that created the rule\.  |  Not intended for use in customer policies\.  | 
|  events:source  |  `"events:source":"source "` Use *source* for the literal string for the source field of the event such as `"aws.ec2"` or `"aws.s3"`\. For more possible values for *source*, see the example events in [Events from AWS services](eb-service-event.md)\.  |  Source, Null  | 
|  events:TargetArn  |  `"events:TargetArn":"target-arn "` For *target\-arn*, use the ARN of the target for the rule, for example `"arn:aws:lambda:*:*:function:*"`\.  |  ArrayOfARN, Null  | 

For example policy statements for EventBridge, see [Managing access permissions to your Amazon EventBridge resources](eb-manage-iam-access.md)\.

**Topics**
+ [EventBridge Pipes specifics](#eb-pipes-condition-diff)
+ [Example: Using the `creatorAccount` condition](#eb-events-creator-account)
+ [Example: Using the `eventBusInvocation` condition](#eb-events-bus-invocation)
+ [Example: Limiting access to a specific source](#eb-events-limit-access-control)
+ [Example: Defining multiple sources that can be used in an event pattern individually](#eb-events-pattern-sources)
+ [Example: Defining a source and a `DetailType `that can be used in an event pattern](#eb-events-pattern-detail-type)
+ [Example: Ensuring that the source is defined in the event pattern](#eb-source-defined-events-pattern)
+ [Example: Defining a list of allowed sources in an event pattern with multiple sources](#eb-allowed-sources-events-pattern)
+ [Example: Limiting `PutRule` access by `detail.service`](#eb-limit-rule-by-service)
+ [Example: Limiting `PutRule` access by `detail.eventTypeCode`](#eb-limit-rule-by-type-code)
+ [Example: Ensuring that only AWS CloudTrail events for API calls from a certain `PrincipalId` are allowed](#eb-consume-specific-events)
+ [Example: Limiting access to targets](#eb-limiting-access-to-targets)

## EventBridge Pipes specifics<a name="eb-pipes-condition-diff"></a>

EventBridge Pipes does not support any additional IAM policy condition keys\.

## Example: Using the `creatorAccount` condition<a name="eb-events-creator-account"></a>

The following example policy statement shows how to use the `creatorAccount` condition in a policy to only allow rules to be created if the account specified as the `creatorAccount` is the account that created the rule\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleForOwnedRules",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEqualsIfExists": {
                    "events:creatorAccount": "${aws:PrincipalAccount}"
                }
            }
        }
    ]
}
```

## Example: Using the `eventBusInvocation` condition<a name="eb-events-bus-invocation"></a>

The `eventBusInvocation` indicates whether the invocation originates from a cross\-account target or a `PutEvents` API request\. The value is **true** when the invocation results from a rule that include a cross\-account target, such as when the target is an event bus in another account\. The value is **false** when the invocation results from a `PutEvents` API request\. The following example indicates an invocation from a cross\-account target\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountInvocationEventsOnly",
      "Effect": "Allow",
      "Action": "events:PutEvents",
      "Resource": "*",
      "Condition": {
        "BoolIfExists": {
          "events:eventBusInvocation": "true"
        }
      }
    }
  ]
}
```

## Example: Limiting access to a specific source<a name="eb-events-limit-access-control"></a>

The following example policies can be attached to an IAM user\. Policy A allows the `PutRule` API action for all events, whereas Policy B allows `PutRule` only if the event pattern of the rule being created matches Amazon EC2 events\.

**Policy A: allow all events**

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleForAllEvents",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*"
        }
    ]
    }
```

**Policy B:—allow events only from Amazon EC2** 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleForAllEC2Events",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": "aws.ec2"
                }
            }
        }
    ]
}
```

`EventPattern` is a mandatory argument to `PutRule`\. Hence, if the user with Policy B calls `PutRule` with an event pattern like the following\.

```
{
    "source": [ "aws.ec2" ]
}
```

The rule would be created because the policy allows for this specific source: that is, `"aws.ec2"`\. However, if the user with Policy B calls `PutRule` with an event pattern like the following, the rule creation would be denied because the policy doesn't allow for this specific source: that is, `"aws.s3"`\.

```
{
    "source": [ "aws.s3" ]
}
```

Essentially, the user with Policy B is only allowed to create a rule that would match the events originating from Amazon EC2; hence, they're only allowed access to the events from Amazon EC2\.

See the following table for a comparison of Policy A and Policy B\.


| Event Pattern | Allowed by Policy A | Allowed by Policy B | 
| --- | --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.s3" ]<br />}</pre>  |  Yes  |  No \(Source aws\.s3 isn't allowed\)  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  |  No \(Source must be specified\)  | 

## Example: Defining multiple sources that can be used in an event pattern individually<a name="eb-events-pattern-sources"></a>

The following policy allows an IAM user or role to create a rule where the source in the `EventPattern` is either Amazon EC2 or Amazon ECS\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsEC2OrECS",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": [ "aws.ec2", "aws.ecs" ]
                }
            }
        }
    ]
}
```

The following table shows some examples of event patterns that are allowed or denied by this policy\.


| Event pattern | Allowed by the policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ecs" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.s3" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.ecs" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ]<br />}</pre>  |  No  | 

## Example: Defining a source and a `DetailType `that can be used in an event pattern<a name="eb-events-pattern-detail-type"></a>

The following policy allows events only from the `aws.ec2` source with `DetailType` equal to `EC2 instance state change notification`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsEC2AndDetailTypeIsInstanceStateChangeNotification",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:source": "aws.ec2",
                    "events:detail-type": "EC2 Instance State-change Notification"
                }
            }
        }
    ]
}
```

The following table shows some examples of event patterns that are allowed or denied by this policy\.


| Event pattern | Allowed by the policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ecs" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance Health Failed" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example: Ensuring that the source is defined in the event pattern<a name="eb-source-defined-events-pattern"></a>

The following policy allows users to only create rules with `EventPatterns` that have the source field\. With this policy, an IAM user or role can't create a rule with an `EventPattern` that doesn't provide a specific source\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsSpecified",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "Null": {
                    "events:source": "false"
                }
            }
        }
    ]
}
```

The following table shows some examples of event patterns that are allowed or denied by this policy\.


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ],<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ecs", "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example: Defining a list of allowed sources in an event pattern with multiple sources<a name="eb-allowed-sources-events-pattern"></a>

The following policy allows users to create rules with `EventPatterns` that have multiple sources in them\. Each source in the event pattern must be a member of the list provided in the condition\. When you use the `ForAllValues` condition, make sure that at least one of the items in the condition list is defined\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleIfSourceIsSpecifiedAndIsEitherS3OrEC2OrBoth",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "ForAllValues:StringEquals": {
                    "events:source": [ "aws.ec2", "aws.s3" ]
                },
                "Null": {
                    "events:source": "false"
                }
            }
        }
    ]
}
```

The following table shows some examples of event patterns that are allowed or denied by this policy\.


| Event Pattern | Allowed by the Policy | 
| --- | --- | 
|  <pre>{<br />    "source": [ "aws.ec2" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.s3" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "source": [ "aws.ec2", "aws.autoscaling" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "EC2 Instance State-change Notification" ]<br />}</pre>  |  No  | 

## Example: Limiting `PutRule` access by `detail.service`<a name="eb-limit-rule-by-service"></a>

You can restrict an IAM user or role to creating rules only for events that have a certain value in the `events:details.service` field\. The value of `events:details.service` isn't necessarily the name of an AWS service\.

This policy condition is helpful when you work with events from AWS Health that relate to security or abuse\. By using this policy condition, you can limit access to these sensitive alerts to only those users who need to see them\.

For example, the following policy allows the creation of rules only for events where the value of `events:details.service` is `ABUSE`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleEventsWithDetailServiceEC2",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail.service": "ABUSE"
                }
            }
        }
    ]
}
```

## Example: Limiting `PutRule` access by `detail.eventTypeCode`<a name="eb-limit-rule-by-type-code"></a>

You can restrict an IAM user or role to creating rules only for events that have a certain value in the `events:details.eventTypeCode` field\. This policy condition is helpful when you work with events from AWS Health that relate to security or abuse\. By using this policy condition, you can limit access to these sensitive alerts to only those users who need to see them\.

 For example, the following policy allows the creation of rules only for events where the value of `events:details.eventTypeCode` is `AWS_ABUSE_DOS_REPORT`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleEventsWithDetailServiceEC2",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail.eventTypeCode": "AWS_ABUSE_DOS_REPORT"
                }
            }
        }
    ]
}
```

## Example: Ensuring that only AWS CloudTrail events for API calls from a certain `PrincipalId` are allowed<a name="eb-consume-specific-events"></a>

All AWS CloudTrail events have the PrincipalId of the user who made the API call in the `detail.userIdentity.principalId` path of an event\. Using the `events:detail.userIdentity.principalId` condition key, you can limit the access of IAM users or roles to the CloudTrail events for only those coming from a specific account\.

```
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutRuleOnlyForCloudTrailEventsWhereUserIsASpecificIAMUser",
            "Effect": "Allow",
            "Action": "events:PutRule",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "events:detail-type": [ "AWS API Call via CloudTrail" ],
                    "events:detail.userIdentity.principalId": [ "AIDAJ45Q7YFFAREXAMPLE" ]
                }
            }
        }
    ]
}
```

The following table shows some examples of event patterns that are allowed or denied by this policy\.


| Event pattern | Allowed by the policy | 
| --- | --- | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ]<br />}</pre>  |  No  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ],<br />    "detail.userIdentity.principalId": [ "AIDAJ45Q7YFFAREXAMPLE" ]<br />}</pre>  |  Yes  | 
|  <pre>{<br />    "detail-type": [ "AWS API Call via CloudTrail" ],<br />    "detail.userIdentity.principalId": [ "AROAIDPPEZS35WEXAMPLE:AssumedRoleSessionName" ]<br />}</pre>  |  No  | 

## Example: Limiting access to targets<a name="eb-limiting-access-to-targets"></a>

If an IAM user or role has `events:PutTargets` permission, they can add any target under the same account to the rules that they are allowed to access\. The following policy limits users to adding targets to only a specific rule: `MyRule` under account `123456789012`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutTargetsOnASpecificRule",
            "Effect": "Allow",
            "Action": "events:PutTargets",
            "Resource": "arn:aws:events:us-east-1:123456789012:rule/MyRule"
        }
    ]
}
```

To limit what target can be added to the rule, use the `events:TargetArn` condition key\. You can limit targets to only Lambda functions, as in the following example\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutTargetsOnASpecificRuleAndOnlyLambdaFunctions",
            "Effect": "Allow",
            "Action": "events:PutTargets",
            "Resource": "arn:aws:events:us-east-1:123456789012:rule/MyRule",
            "Condition": {
                "ArnLike": {
                    "events:TargetArn": "arn:aws:lambda:*:*:function:*"
                }
            }
        }
    ]
}
```