# Permissions for Amazon EventBridge event buses<a name="eb-event-bus-perms"></a>

The default [event bus](eb-event-bus.md) in your AWS account only allows [events](eb-events.md) from one account\. You can grant additional permissions to an event bus by attaching a [resource\-based policy](eb-use-resource-based.md) to it\. With a resource\-based policy, you can allow `PutEvents`, `PutRule`, and `PutTargets` API calls from another account\. You can also use [IAM conditions](eb-use-conditions.md) in the policy to grant permissions to an organization, apply [tags](eb-tagging.md), or filter events to only those from a specific rule or account\. You can set a resource\-based policy for an event bus when you create it or afterward\.

EventBridge APIs that accept an event bus `Name` parameter such as `PutRule`, `PutTargets`, `DeleteRule`, `RemoveTargets`, `DisableRule`, and `EnableRule` also accept the event bus ARN\. Use these parameters to reference cross\-account or cross\-Region event buses through the APIs\. For example, you can call `PutRule` to create a [rule](eb-rules.md) on an event bus in a different account without needing to assume a role\.

You can use the example policies in this topic to grant permission to send events to a different account or Region\. For information about creating a rule to send events to a different account or Region, see [Sending and receiving Amazon EventBridge events between AWS accounts and Regions](eb-cross-account.md)\.

**Topics**
+ [Managing event bus permissions](#eb-event-bus-permissions-manage)
+ [Example policy: Send events to the default bus in a different account](#eb-event-bus-example-policy-cross-account)
+ [Example policy: Send events to a custom bus in a different account](#eb-event-bus-example-policy-cross-account-custom-bus-source)
+ [Example policy: Send events to the same account and restrict updates](#eb-event-bus-example-policy-same-account-creator)
+ [Example policy: Send events only from a specific rule to the bus in a different Region](#eb-event-bus-example-policy-restrict-rule)
+ [Example policy: Send events only from a specific Region to a different Region](#eb-event-bus-example-policy-specific-region)
+ [Example policy: Deny sending events from specific Regions](#eb-event-bus-example-policy-deny-regions)

## Managing event bus permissions<a name="eb-event-bus-permissions-manage"></a>

Use the following procedure to modify the permissions for an existing event bus\. For information about how to use AWS CloudFormation to create an event bus policy, see [AWS::Events::EventBusPolicy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-eventbuspolicy.html)\.

**To manage permissions for an existing event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the left navigation pane, choose **Event buses**\.

1. In **Name**, choose the name of the event bus to manage permissions for\.

   If a resource policy is attached to the event bus, the policy displays\.

1. Choose **Manage permissions**, and then do one of the following:
   + Enter the policy that includes the permissions to grant for the event bus\. You can paste in a policy from another source, or enter the JSON for the policy\.
   + To use a template for the policy, choose **Load template**\. Modify the policy as appropriate for your environment, and add additional actions that you authorize the principal in the policy to use\.

1. Choose **Update**\.

The template provides example policy statements that you can customize for your account and environment\. The template isn't a valid policy\. You can modify the template for your use case, or you can copy one of the example policies and customize it\.

The template loads policies that include an example of how to grant permissions to an account to use the `PutEvents` action, how to grant permissions to an organization, and how to grant permissions to the account to manage rules in the account\. You can customize the template for your specific account, and then delete the other sections from the template\. More example policies are included later in this topic\.

If you try to update the permissions for the bus but the policy contains an error, an error message indicates the specific issue in the policy\.

```
  ### Choose which sections to include in the policy to match your use case. ###
  ### Be sure to remove all lines that start with ###, including the ### at the end of the line. ###

  ### The policy must include the following: ###

  {
    "Version": "2012-10-17",
    "Statement": [

      ### To grant permissions for an account to use the PutEvents action, include the following, otherwise delete this section: ###

      {

        "Sid": "allow_account_to_put_events",
        "Effect": "Allow",
        "Principal": {
          "AWS": "<ACCOUNT_ID>"
        },
        "Action": "events:PutEvents",
        "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/default"
      },

      ### Include the following section to grant permissions to all members of your AWS Organizations to use the PutEvents action ###

      {
        "Sid": "allow_all_accounts_from_organization_to_put_events",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "events:PutEvents",
        "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/default",
        "Condition": {
          "StringEquals": {
            "aws:PrincipalOrgID": "o-yourOrgID"
          }
        }
      },

      ### Include the following section to grant permissions to the account to manage the rules created in the account ###

      {
        "Sid": "allow_account_to_manage_rules_they_created",
        "Effect": "Allow",
        "Principal": {
          "AWS": "<ACCOUNT_ID>"
        },
        "Action": [
          "events:PutRule",
          "events:PutTargets",
          "events:DeleteRule",
          "events:RemoveTargets",
          "events:DisableRule",
          "events:EnableRule",
          "events:TagResource",
          "events:UntagResource",
          "events:DescribeRule",
          "events:ListTargetsByRule",
          "events:ListTagsForResource"],
        "Resource": "arn:aws:events:us-east-1:123456789012:rule/default",
        "Condition": {
          "StringEqualsIfExists": {
            "events:creatorAccount": "<ACCOUNT_ID>"
          }
        }
    }]
  }
```

## Example policy: Send events to the default bus in a different account<a name="eb-event-bus-example-policy-cross-account"></a>

The following example policy grants the account 111122223333 permission to use all API operations on the default event bus in the account 123456789012\.

```
{
   "Version": "2012-10-17",
   "Statement": [
       {
        "Sid": "sid1",
        "Effect": "Allow",
        "Principal": {"AWS":"arn:aws:iam::111112222333:root"},
        "Action": "events:*",
        "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/default"
        }
    ]
  }
```

## Example policy: Send events to a custom bus in a different account<a name="eb-event-bus-example-policy-cross-account-custom-bus-source"></a>

The following example policy grants the account 111122223333 permission to publish events to the `central-event-bus` in account 123456789012, but only for events with a source value set to `com.exampleCorp.webStore` and a `detail-type` set to `newOrderCreated`\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "WebStoreCrossAccountPublish",
      "Effect": "Allow",
      "Action": [
        "events:PutEvents"
      ],
      "Principal": {
        "AWS": "111112222333"
      },
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/central-event-bus",
      "Condition": {
        "StringEquals": {
          "events:detail-type": "newOrderCreated",
          "events:source": "com.exampleCorp.webStore"
        }
      }
    }
  ]
}
```

## Example policy: Send events to the same account and restrict updates<a name="eb-event-bus-example-policy-same-account-creator"></a>

The following example policy grants account 123456789012 permission to create, delete, update, disable and enable rules, and add or remove targets\. It limits these rules that match against events with a source of `com.exampleCorp.webStore`, and it uses the `"events:creatorAccount": "${aws:PrincipalAccount}"` to ensure that only account 123456789012 can modify these rules and targets once they have been created\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "InvoiceProcessingRuleCreation",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123412341234:root"
      },
      "Action": [
        "events:PutRule",
        "events:DeleteRule",
        "events:DescribeRule",
        "events:DisableRule",
        "events:EnableRule",
        "events:PutTargets",
        "events:RemoveTargets"
      ],
      "Resource": "arn:aws:events:us-east-1:123412341234:rule/central-event-bus/*",
      "Condition": {
        "StringEqualsIfExists": {
          "events:creatorAccount": "${aws:PrincipalAccount}",
          "events:source": "com.exampleCorp.webStore"
        }
      }
    }
  ]
}
```

## Example policy: Send events only from a specific rule to the bus in a different Region<a name="eb-event-bus-example-policy-restrict-rule"></a>

The following example policy grants the account 111122223333 permission to send events that match a rule named `SendToUSE1AnotherAccount` in the Middle East \(Bahrain\) and US West \(Oregon\) Regions to an event bus named `CrossRegionBus` in the US East \(N\. Virginia\) in account 123456789012\. The example policy is added to the event bus named `CrossRegionBus` in account 123456789012\. The policy allows events only if they match a rule specified for the event bus in account 111122223333\. The `Condition` statement restricts events to only events that match the rules with the specified rule ARN\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "allow_specific_rules_as_cross_region_source",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111112222333:root"
      },
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/CrossRegionBus",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": [
            "arn:aws:events:us-west-2:111112222333:rule/CrossRegionBus/SendToUSE1AnotherAccount",
            "arn:aws:events:me-south-1:111112222333:rule/CrossRegionBus/SendToUSE1AnotherAccount"
          ]
        }
      }
    }
  ]
}
```

## Example policy: Send events only from a specific Region to a different Region<a name="eb-event-bus-example-policy-specific-region"></a>

The following example policy grants account 111122223333 permission to send all events that are generated in the Middle East \(Bahrain\) and US West \(Oregon\) Regions to the event bus named `CrossRegionBus` in account 123456789012 in the US East \(N\. Virginia\) Region\. Account 111122223333 doesn't have permission to send events that are generated in any other Region\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "allow_cross_region_events_from_us-west-2_and_me-south-1",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111112222333:root"
      },
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/CrossRegionBus",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": [
            "arn:aws:events:us-west-2:*:*",
            "arn:aws:events:me-south-1:*:*"
          ]
        }
      }
    }
  ]
}
```

## Example policy: Deny sending events from specific Regions<a name="eb-event-bus-example-policy-deny-regions"></a>

The following example policy attached to an event bus named `CrossRegionBus` in account 123456789012 grants permission for the event bus to receive events from the account 111122223333, but not events that are generated in the US West \(Oregon\) Region\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "1_allow_any_events_from_account_111112222333",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111112222333:root"
      },
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/CrossRegionBus"
    },
    {
      "Sid": "2_deny-all-cross-region-us-west-2-events",
      "Effect": "Deny",
      "Principal": {
        "AWS": "*"
      },
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:123456789012:event-bus/CrossRegionBus",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": [
            "arn:aws:events:us-west-2:*:*"
          ]
        }
      }
    }
  ]
}
```