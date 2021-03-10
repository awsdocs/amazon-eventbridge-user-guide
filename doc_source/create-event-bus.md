# Creating an Event Bus<a name="create-event-bus"></a>

Your account includes one default event bus, which receives events emitted by AWS services\. You can also configure your custom applications to send events to the default event bus\.

You can create two types of additional event buses in your account:
+ *Partner event buses*, which can receive events from applications and services created by AWS software as a service \(SaaS\) partners\. To receive events from SaaS partners, you need to create a partner event bus for each partner event source that you want to receive events from\.

  For more information, see [Receiving events from a SaaS partner](create-partner-event-bus.md)\.
+ *Custom event buses*, which can receive events from your custom applications and services\.

  Each event bus in your account can have up to 100 EventBridge rules associated with it, so if your account has many rules, you might want to create custom event buses to associate with some of the rules for your custom application events\. Another reason to create custom event buses is to apply different permissions to different event buses\. When you set permissions on an event bus, you can specify which other accounts or entire organizations can send events to the event bus\.

## Event bus permissions<a name="event-bus-perms"></a>

The default event bus in your account restricts permissions to only the account that the bus is created in\. You can grant additional permissions to the default bus and any additional buses you create by attaching a resource\-based policy to the bus\. With a resource\-based policy, you can reference an event bus in another account when granting permissions to allow `PutEvents`, `PutRule`, and `PutTargets` API calls\. You can also use conditions in the policy to grant permissions to an AWS Organization or apply Tags as appropriate for your environment\. You set the resource\-based policy for an event bus when you create it, or you can manage permissions for an existing event bus\.

EventBridge APIs that accept an event bus `Name` parameter \(including `PutRule`, `PutTargets`, `DeleteRule`, `RemoveTargets`, `DisableRule`, and `EnableRule`\) now also support providing the event bus ARN\. This allows you to target cross\-account event buses through the APIs\. For example, you can call `PutRule` to create a rule on an event bus in another account, without needing to assume a role\.

**To manage permissions for an existing event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Event buses** in the left navigation pane\.

1. Choose the name of the bus in the **Name** to manage permissions for\.

   If a resource policy is attached to the event bus, the policy is displayed\.

1. Choose **Manage permissions**, then do one of the following:
   + Enter the policy that includes the permissions to grant for the event bus\. You can paste in a policy from another source, or enter the JSON for the policy\.
   + To use a template for the policy, choose **Load template**\. Modify the policy as appropriate for your environment, including adding additional actions that you authorize the principal in the policy to use\.

1. Choose **Update**\.

The template provided includes only the basic structure of the policy to attach to the bus\. The template is not a valid policy\. You must make the following changes to successfully attach the policy to your event bus\. If you are comfortable making policies, you can modify the template as appropriate for your use case\. If not, you can copy one of the example policies and customize it\.

This is the template that loads:

```
{
  "Version": "2012-10-17",
  "Statement": [{
      "Sid": "",
      "Effect": "",
      "Principal": {
        "AWS": ""
      },
      "Action": "events:PutEvents",
      "Resource": ""
    },
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "events:PutEvents",
      "Resource": "",
      "Condition": {
        "StringEquals": {}
      }
    }
  ]
}
```

The policy includes permission only for the `PutEvents` action\. Also notice that there are a few values missing and show only quotation marks, such as `"Effect": ""`\. These are the values you add to the policy so that it works for your account or environment\. If you try to update the permissions for the bus and the policy contains an error, an error message indicates the specific issue in the policy\.

Here is an example policy that grants permission for the AWS account 111112222333 to use all API operations against the default event bus in the AWS account 123456789012:

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
}
```

The following example demonstrates a policy that grants Account A permissions to publish events to the `central-event-bus` in account B, but only for events with a source value set to `com.exampleCorp.webStore` and a `detail-type` set to `newOrderCreated`\.

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
        "AWS": "[ACCOUNT-A]"
      },
      "Resource": "arn:aws:events:us-east-1:[ACCOUNT-B]:event-bus/central-event-bus",
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

This example demonstrates a policy that provides access for account C to create, delete, update, disable and enable rules, and targets\. It limits these rules that match against events with a source of `com.exampleCorp.webStore`, and it uses the `"events:creatorAccount": "${aws:PrincipalAccount}"` to ensure that only account C can modify these rules and targets once they have been created

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "InvoiceProcessingRuleCreation",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::[ACCOUNT-C]:root"
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
      "Resource": "arn:aws:events:us-east-1:[ACCOUNT-B]:rule/central-event-bus/*",
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

## Creating a Custom Event Bus<a name="create-custom-event-bus"></a>

You can create a custom event bus to receive events from your custom applications\. Your applications can also send events to your default event bus\. When you create an event bus, you can attach a resource\-based policy to grant permissions to other accounts to enable cross\-account targets\. This lets you authorized other accounts to send events to the event bus in the current account\.

**To create a custom event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. Choose **Create event bus**\.

1. Enter a name for the new event bus\.

1. Do one of the following:
   + Enter the policy that includes the permissions to grant for the event bus\. You can paste in a policy from another source, or enter the JSON for the policy\. You can use one of the example policies and modify it for your environment\.
   + To use a template for the policy, choose **Load template**\. Modify the policy as appropriate for your environment, including adding additional actions that you authorize the principal in the policy to use\.

1. Choose **Create**\.