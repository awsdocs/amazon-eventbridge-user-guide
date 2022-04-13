# Troubleshooting Amazon EventBridge<a name="eb-troubleshooting"></a>

You can use the steps in this section to troubleshoot Amazon EventBridge\.

**Topics**
+ [My rule ran but my Lambda function wasn't invoked](#eb-lam-function-not-invoked)
+ [I just created or modified a rule, but it didn't match a test event](#eb-rule-does-not-match)
+ [My rule didn't run at the time I specified in the `ScheduleExpression`](#eb-rule-did-not-trigger)
+ [My rule didn't run at the time that I expected](#eb-rule-did-not-trigger-on-time)
+ [My rule matches IAM API calls but it didn't run](#eb-rule-did-not-trigger-iam)
+ [The IAM role associated with my rule is being ignored when the rule runs](#eb-iam-role-ignored)
+ [My rule has an event pattern that is supposed to match a resource, but no events match](#eb-events-do-not-match-rule)
+ [My event's delivery to the target was delayed](#eb-delayed-event-delivery)
+ [Some events were never delivered to my target](#eb-never-delivered-to-target)
+ [My rule ran more than once in response to one event](#eb-rule-triggered-more-than-once)
+ [Preventing infinite loops](#eb-prevent-infinite-loops)
+ [My events are not delivered to the target Amazon SQS queue](#eb-sqs-encrypted)
+ [My rule runs, but I don't see any messages published into my Amazon SNS topic](#eb-no-messages-published-sns)
+ [My Amazon SNS topic still has permissions for EventBridge even after I deleted the rule associated with the Amazon SNS topic](#eb-sns-permissions-persist)
+ [Which IAM condition keys can I use with EventBridge?](#eb-supported-access-policies)
+ [How can I tell when EventBridge rules are broken?](#eb-create-alarm-broken-event-rules)

## My rule ran but my Lambda function wasn't invoked<a name="eb-lam-function-not-invoked"></a>

One reason your Lambda function might not run is if you don't have the right permissions\. 

**To check your permissions for your Lambda function**

1. Using the AWS CLI, run the following command with your function and your AWS Region:

   ```
   aws lambda get-policy --function-name MyFunction --region us-east-1
   ```

   You should see the following output\.

   ```
   {
       "Policy": "{\"Version\":\"2012-10-17\",
       \"Statement\":[
           {\"Condition\":{\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:events:us-east-1:123456789012:rule/MyRule\"}},
           \"Action\":\"lambda:InvokeFunction\",
           \"Resource\":\"arn:aws:lambda:us-east-1:123456789012:function:MyFunction\",
           \"Effect\":\"Allow\",
           \"Principal\":{\"Service\":\"events.amazonaws.com\"},
           \"Sid\":\"MyId\"}
       ],
       \"Id\":\"default\"}"
   }
   ```

1. If you see the following message\.

   ```
   A client error (ResourceNotFoundException) occurred when calling the GetPolicy operation: The resource you requested does not exist.
   ```

   Or, you see the output but you can't locate events\.amazonaws\.com as a trusted entity in the policy, run the following command:

   ```
   aws lambda add-permission \
   --function-name MyFunction \
   --statement-id MyId \
   --action 'lambda:InvokeFunction' \
   --principal events.amazonaws.com \
   --source-arn arn:aws:events:us-east-1:123456789012:rule/MyRule
   ```

1. If the output contains a `SourceAccount` field, then you need to remove it\. A `SourceAccount` setting prevents EventBridge from being able to invoke the function\.

**Note**  
If the policy is incorrect, you can edit the [rule](eb-rules.md) in the EventBridge console by removing and then adding it back to the rule\. The EventBridge console then sets the correct permissions on the [target](eb-targets.md)\.  
If you're using a specific Lambda alias or version, add the `--qualifier` parameter in the `aws lambda get-policy` and `aws lambda add-permission` commands, as shown in the following command   

```
aws lambda add-permission \
--function-name MyFunction \
--statement-id MyId \
--action 'lambda:InvokeFunction' \
--principal events.amazonaws.com \
--source-arn arn:aws:events:us-east-1:123456789012:rule/MyRule
--qualifier alias or version
```

## I just created or modified a rule, but it didn't match a test event<a name="eb-rule-does-not-match"></a>

When you make a change to a [rule](eb-rules.md) or to its [targets](eb-targets.md), incoming [events](eb-events.md) might not immediately start or stop matching to new or updated rules\. Allow a short period of time for changes to take effect\. 

If events still don't match after a short period of time, check the CloudWatch metrics `TriggeredRules`, `Invocations`, and `FailedInvocations` for your rule\. For more information about these metrics, see [Monitoring Amazon EventBridge](eb-monitoring.md)\.

If the rule is intended to match an event from an AWS service, use the `TestEventPattern` action to test the event pattern of your rule matches a test event\. For more information, see [TestEventPattern](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TestEventPattern.html) in the *Amazon EventBridge API Reference*\.

## My rule didn't run at the time I specified in the `ScheduleExpression`<a name="eb-rule-did-not-trigger"></a>

Make sure you have set the schedule for the [rule](eb-rules.md) in the UTC\+0 time zone\. If the `ScheduleExpression` is correct, then follow the steps under [I just created or modified a rule, but it didn't match a test event](#eb-rule-does-not-match)\.

## My rule didn't run at the time that I expected<a name="eb-rule-did-not-trigger-on-time"></a>

EventBridge runs [rules](eb-rules.md) within one minute of the start time you set\. The count down to run time begins as soon as you create the rule\.

You can use a cron expression to invoke [targets](eb-targets.md) at a specified time\. To create a rule that runs every four hours on the 0th minute, you do one of the following:
+ In the EventBridge console, you use the cron expression `0 0/4 * * ? *`\.
+ Using the AWS CLI, you use the expression `cron(0 0/4 * * ? *)`\.

For example, to create a rule named `TestRule` that runs every 4 hours by using the AWS CLI, you use the following command\.

```
aws events put-rule --name TestRule --schedule-expression 'cron(0 0/4 * * ? *)'
```

To run a rule every five minutes, you use the following cron expressio\.

```
aws events put-rule --name TestRule --schedule-expression 'cron(0/5 * * * ? *)'
```

The finest resolution for an EventBridge rule that uses a cron expression is one minute\. Your scheduled rule runs within that minute but not on the precise 0th second\.

Because EventBridge and target services are distributed, there can be a delay of several seconds between the time the scheduled rule runs and the time the target service performs the action on the target resource\.

## My rule matches IAM API calls but it didn't run<a name="eb-rule-did-not-trigger-iam"></a>

The IAM service is only available in the US East \(N\. Virginia\) Region, so events from AWS API calls from IAM are only available in that region\. For more information, see [Events from AWS services](eb-service-event.md)\.

## The IAM role associated with my rule is being ignored when the rule runs<a name="eb-iam-role-ignored"></a>

EventBridge only uses IAM roles for [rules](eb-rules.md) that send [events](eb-events.md) to Kinesis streams\. For rules that invoke Lambda functions or Amazon SNS topics, you need to provide [resource\-based permissions](eb-use-resource-based.md)\.

Make sure your regional AWS STS endpoints are enabled, so that EventBridge can use them when assuming the IAM role you provided\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.

## My rule has an event pattern that is supposed to match a resource, but no events match<a name="eb-events-do-not-match-rule"></a>

Most services in AWS treat a colon \(:\) or slash \(/\) as the same character in Amazon Resource Names \(ARNs\)\., but EventBridge uses an exact match in [event patterns](eb-event-patterns.md) and [rules](eb-rules.md)\. Be sure to use the correct ARN characters when creating event patterns so that they match the ARN syntax in the [event](eb-events.md) to match\.

Some events, such as AWS API call events from CloudTrail, don't have anything in the resources field\.

## My event's delivery to the target was delayed<a name="eb-delayed-event-delivery"></a>

EventBridge tries to deliver an [event](eb-events.md) to a [target](eb-targets.md) for up to 24 hours, except in scenarios where your target resource is constrained\. The first attempt is made as soon as the event arrives in the event stream\. If the target service is having problems, EventBridge automatically reschedules another delivery\. If 24 hours has passed since the arrival of event, EventBridge stops trying to deliver the event and publishes the `FailedInvocations` metric in CloudWatch\. We recommend that you create a CloudWatch alarm on the `FailedInvocations` metric\. 

## Some events were never delivered to my target<a name="eb-never-delivered-to-target"></a>

If the [target](eb-targets.md) of an EventBridge [rule](eb-rules.md) is constrained for a prolonged time, EventBridge might not retry delivery\. For example, if the target is not provisioned to handle the incoming [event](eb-events.md) traffic and the target service is throttling requests that EventBridge makes on your behalf, then EventBridge might not retry delivery\.

## My rule ran more than once in response to one event<a name="eb-rule-triggered-more-than-once"></a>

In rare cases, the same [rule](eb-rules.md) can run more than once for a single [event](eb-events.md) or scheduled time, or the same [target](eb-targets.md) can be invoked more than once for a given triggered rule\. 

## Preventing infinite loops<a name="eb-prevent-infinite-loops"></a>

In EventBridge, it is possible to create a [rule](eb-rules.md) that leads to infinite loops, where the rule runs repeatedly\. If you have a rule that causes an infinite loop, rewrite it so that the actions that the rule takes don't match the same rule\. 

For example, a rule that detects that ACLs have changed on an Amazon S3 bucket and then runs software to change them to a new state causes an infinite loop\. One way to resolve it is to rewrite the rule so that it only matches ACLs that are in a bad state\. 

An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified limit\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

## My events are not delivered to the target Amazon SQS queue<a name="eb-sqs-encrypted"></a>

If your Amazon SQS queue is encrypted, you must must create a customer managed KMS key and add include the following permission section in your KMS key policy. For more information, see [Configureing AWS KMS permissions](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-key-management.html#sqs-what-permissions-for-sse)

```
{
    "Sid": "Allow EventBridge to use the key",
    "Effect": "Allow",
    "Principal": {
        "Service": "events.amazonaws.com"
    },
    "Action": [
        "kms:Decrypt",
        "kms:GenerateDataKey"
    ],
    "Resource": "*"
}
```

## My rule runs, but I don't see any messages published into my Amazon SNS topic<a name="eb-no-messages-published-sns"></a>

**Scenario 1**

You need permission for messages to be published into your Amazon SNS topic\. Use the following command using the AWS CLI, replacing us\-east\-1 with your Region and using your topic ARN\.

```
aws sns get-topic-attributes --region us-east-1 --topic-arn "arn:aws:sns:us-east-1:123456789012:MyTopic"
```

To have the correct permission, your policy attributes similar to the following\.

```
"{\"Version\":\"2012-10-17\",
\"Id\":\"__default_policy_ID\",
\"Statement\":[{\"Sid\":\"__default_statement_ID\",
\"Effect\":\"Allow\",
\"Principal\":{\"AWS\":\"*\"},
\"Action\":[\"SNS:Subscribe\",
\"SNS:ListSubscriptionsByTopic\",
\"SNS:DeleteTopic\",
\"SNS:GetTopicAttributes\",
\"SNS:Publish\",
\"SNS:RemovePermission\",
\"SNS:AddPermission\",
\"SNS:Receive\",
\"SNS:SetTopicAttributes\"],
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\",
\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"123456789012\"}}},{\"Sid\":\"Allow_Publish_Events\",
\"Effect\":\"Allow\",
\"Principal\":{\"Service\":\"events.amazonaws.com\"},
\"Action\":\"sns:Publish\",
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\"}]}"
```

If you don't see `events.amazonaws.com` with `Publish` permission in your policy, first copy the current policy and add the following statement to the list of statements\.

```
{\"Sid\":\"Allow_Publish_Events\",
\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},
\"Action\":\"sns:Publish\",
\"Resource\":\"arn:aws:sns:us-east-1:123456789012:MyTopic\"}
```

Then set the topic attributes by using the AWS CLI, use the following command\.

```
aws sns set-topic-attributes --region us-east-1 --topic-arn "arn:aws:sns:us-east-1:123456789012:MyTopic" --attribute-name Policy --attribute-value NEW_POLICY_STRING
```

**Note**  
If the policy is incorrect, you can also edit the [rule](eb-rules.md) in the EventBridge console by removing and then adding it back to the rule\. EventBridge sets the correct permissions on the [target](eb-targets.md)\.

**Scenario 2**

If your SNS topic is encrypted, you must include the following section in your KMS key policy\.

```
{
                "Sid": "Allow CWE to use the key",
                "Effect": "Allow",
                "Principal": {
                                "Service": "events.amazonaws.com"
                },
                "Action": [
                                "kms:Decrypt",
                                "kms:GenerateDataKey"
                ],
                "Resource": "*"
}
```

## My Amazon SNS topic still has permissions for EventBridge even after I deleted the rule associated with the Amazon SNS topic<a name="eb-sns-permissions-persist"></a>

When you create a [rule](eb-rules.md) with Amazon SNS as the [target](eb-targets.md), EventBridge adds permission to your Amazon SNS topic on your behalf\. If you delete the rule shortly after you create it, EventBridge might not remove the permission from your Amazon SNS topic\. If this happens, you can remove the permission from the topic by using the `aws sns set-topic-attributes` command\. For information about resource\-based permissions for sending events, see [Using resource\-based policies for Amazon EventBridge](eb-use-resource-based.md)\.

## Which IAM condition keys can I use with EventBridge?<a name="eb-supported-access-policies"></a>

EventBridge supports the AWS\-wide condition keys \(see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/AvailableKeys.html) in the *IAM User Guide*\), plus the following service\-specific condition keys\. For more information, see [Using IAM policy conditions for fine\-grained access control](eb-use-conditions.md)\.

## How can I tell when EventBridge rules are broken?<a name="eb-create-alarm-broken-event-rules"></a>

You can use the following alarm to notify you when your EventBridge [rules](eb-rules.md) are broken\.

**To create an alarm to alert when rules are broken**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Create Alarm**\. In the **CloudWatch Metrics by Category** pane, choose **Events Metrics**\.

1. In the list of metrics, select **FailedInvocations**\.

1. Above the graph, choose **Statistic**, **Sum**\.

1. For **Period**, choose a value, for example **5 minutes**\. Choose **Next**\.

1. Under **Alarm Threshold**, for **Name**, type a unique name for the alarm, for example **myFailedRules**\. For **Description**, type a description of the alarm, for example **Rules aren't delivering events to targets**\.

1. For **is**, choose **>=** and **1**\. For **for**, enter **10**\.

1. Under **Actions**, for **Whenever this alarm**, choose **State is ALARM**\.

1. For **Send notification to**, select an existing Amazon SNS topic or create a new one\. To create a new topic, choose **New list**\. Type a name for the new Amazon SNS topic, for example: **myFailedRules**\. 

1. For **Email list**, type a comma\-separated list of email addresses to be notified when the alarm changes to the **ALARM** state\.

1. Choose **Create Alarm**\.
