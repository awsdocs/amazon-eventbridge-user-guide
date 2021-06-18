# Using resource\-based policies for Amazon EventBridge<a name="eb-use-resource-based"></a>

When a [rule](eb-rules.md) runs in EventBridge, all of the [targets](eb-targets.md) associated with the rule are invoked\. Rules can invoke AWS Lambda functions, publish to Amazon SNS topics, or relay the event to Kinesis streams\. To make API calls against the resources you own, EventBridge needs the appropriate permissions\. For Lambda, Amazon SNS, Amazon SQS, and Amazon CloudWatch Logs resources, EventBridge uses resource\-based policies\. For Kinesis streams, EventBridge uses [identity\-based](eb-use-identity-based.md) policies\.

You use the AWS CLI to add permissions to your targets\. For information about how to install and configure the AWS CLI, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

**Topics**
+ [Amazon API Gateway permissions](#eb-api-gateway-permissions)
+ [CloudWatch Logs permissions](#eb-cloudwatchlogs-permissions)
+ [AWS Lambda permissions](#eb-lambda-permissions)
+ [Amazon SNS permissions](#eb-sns-permissions)
+ [Amazon SQS permissions](#eb-sqs-permissions)

## Amazon API Gateway permissions<a name="eb-api-gateway-permissions"></a>

To invoke your Amazon API Gateway endpoint by using a EventBridge rule, add the following permission to the policy of your API Gateway endpoint\.

```
{
  "Version": "2012-10-17",
  "Statement": [
       {
           "Effect": "Allow",
           "Principal": {
               "Service": "events.amazonaws.com"
           },
           "Action": "execute-api:Invoke",
           "Condition": {
               "ArnEquals": {
                   "aws:SourceArn": "arn:aws:events:region:account-id:rule/rule-name"
           },
           "Resource": [
               "execute-api:/stage/GET/api"
           ]
       }
  ]
}
```

## CloudWatch Logs permissions<a name="eb-cloudwatchlogs-permissions"></a>

When CloudWatch Logs is the target of a rule, EventBridge creates log streams, and CloudWatch Logs stores the text from the events as log entries\. To allow EventBridge to create the log stream and log the events, CloudWatch Logs must include a resource\-based policy that enables EventBridge to write to CloudWatch Logs\.

If you use the AWS Management Console to add CloudWatch Logs as the target of a rule, the resource\-based policy is created automatically\. If you use the AWS CLI to add the target, and the policy doesn't already exist, you must create it\.

The following example allows EventBridge to write to all log groups that have names that start with `/aws/events/`\. If you use a different naming policy for these types of logs, adjust the example accordingly\.

```
{
    "Statement": [
        {
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Effect": "Allow",
            "Principal": {
                "Service": "events.amazonaws.com"
                "Service": "delivery.logs.amazonaws.com"
            },
            "Resource": "arn:aws:logs:region:account:log-group:/aws/events/*:*",
            "Sid": "TrustEventsToStoreLogEvent"
        }
    ],
    "Version": "2012-10-17"
}
```

For more information, see [PutResourcePolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutResourcePolicy.html) in the *CloudWatch Logs API Reference guide*\.

## AWS Lambda permissions<a name="eb-lambda-permissions"></a>

To invoke your AWS Lambda function by using a EventBridge rule, add the following permission to the policy of your Lambda function\.

```
{
  "Effect": "Allow",
  "Action": "lambda:InvokeFunction",
  "Resource": "arn:aws:lambda:region:account-id:function:function-name",
  "Principal": {
    "Service": "events.amazonaws.com"
  },
  "Condition": {
    "ArnLike": {
      "AWS:SourceArn": "arn:aws:events:region:account-id:rule/rule-name"
    }
  },
  "Sid": "InvokeLambdaFunction"
}
```

**To add the above permissions that enable EventBridge to invoke Lambda functions using the AWS CLI**
+ At a command prompt, enter the following command\.

  ```
  aws lambda add-permission --statement-id "InvokeLambdaFunction" \
  --action "lambda:InvokeFunction" \
  --principal "events.amazonaws.com" \
  --function-name "arn:aws:lambda:region:account-id:function:function-name" \
  --source-arn "arn:aws:events:region:account-id:rule/rule-name"
  ```

For more information about setting permissions that enable EventBridge to invoke Lambda functions, see [AddPermission](https://docs.aws.amazon.com/lambda/latest/dg/API_AddPermission.html) and [Using Lambda with Scheduled Events](https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html) in the *AWS Lambda Developer Guide*\.

## Amazon SNS permissions<a name="eb-sns-permissions"></a>

To allow EventBridge to publish an Amazon SNS topic, use the `aws sns get-topic-attributes` and the `aws sns set-topic-attributes` commands\.

**Note**  
You can't use of `Condition` blocks in Amazon SNS topic policies for EventBridge\.

**To add permissions that enable EventBridge to publish SNS topics**

1. To list the attributes of an SNS topic, use the following command\.

   ```
   aws sns get-topic-attributes --topic-arn "arn:aws:sns:region:account-id:topic-name"
   ```

   The following example shows the result of a new SNS topic\.

   ```
   {
       "Attributes": {
           "SubscriptionsConfirmed": "0", 
           "DisplayName": "", 
           "SubscriptionsDeleted": "0", 
           "EffectiveDeliveryPolicy": "{\"http\":{\"defaultHealthyRetryPolicy\":{\"minDelayTarget\":20,\"maxDelayTarget\":20,\"numRetries\":3,\"numMaxDelayRetries\":0,\"numNoDelayRetries\":0,\"numMinDelayRetries\":0,\"backoffFunction\":\"linear\"},\"disableSubscriptionOverrides\":false}}",
           "Owner": "account-id", 
           "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"__default_policy_ID\",\"Statement\":[{\"Sid\":\"__default_statement_ID\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"*\"},\"Action\":[\"SNS:GetTopicAttributes\",\"SNS:SetTopicAttributes\",\"SNS:AddPermission\",\"SNS:RemovePermission\",\"SNS:DeleteTopic\",\"SNS:Subscribe\",\"SNS:ListSubscriptionsByTopic\",\"SNS:Publish\",\"SNS:Receive\"],\"Resource\":\"arn:aws:sns:region:account-id:topic-name\",\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"account-id\"}}}]}", 
           "TopicArn": "arn:aws:sns:region:account-id:topic-name", 
           "SubscriptionsPending": "0"
       }
   }
   ```

1. Use a [JSON to string converter](https://tools.knowledgewalls.com/jsontostring) to convert the following statement to a string\.

   ```
   {
     "Sid": "PublishEventsToMyTopic",
     "Effect": "Allow",
     "Principal": {
       "Service": "events.amazonaws.com"
     },
     "Action": "sns:Publish",
     "Resource": "arn:aws:sns:region:account-id:topic-name"
   }
   ```

   After you convert the statement to a string, it looks like the following example\.

   ```
   {\"Sid\":\"PublishEventsToMyTopic\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"sns:Publish\",\"Resource\":\"arn:aws:sns:region:account-id:topic-name\"}
   ```

1. Add the string you created in the previous step to the `"Statement"` collection inside the `"Policy"` attribute\.

1. Use the `aws sns set-topic-attributes` command to set the new policy\.

   ```
   aws sns set-topic-attributes --topic-arn "arn:aws:sns:region:account-id:topic-name" \
   --attribute-name Policy \
   --attribute-value "{\"Version\":\"2012-10-17\",\"Id\":\"__default_policy_ID\",\"Statement\":[{\"Sid\":\"__default_statement_ID\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"*\"},\"Action\":[\"SNS:GetTopicAttributes\",\"SNS:SetTopicAttributes\",\"SNS:AddPermission\",\"SNS:RemovePermission\",\"SNS:DeleteTopic\",\"SNS:Subscribe\",\"SNS:ListSubscriptionsByTopic\",\"SNS:Publish\",\"SNS:Receive\"],\"Resource\":\"arn:aws:sns:region:account-id:topic-name\",\"Condition\":{\"StringEquals\":{\"AWS:SourceOwner\":\"account-id\"}}}, {\"Sid\":\"PublishEventsToMyTopic\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"sns:Publish\",\"Resource\":\"arn:aws:sns:region:account-id:topic-name\"}]}"
   ```

For more information, see the [SetTopicAttributes](https://docs.aws.amazon.com/sns/latest/api/API_SetTopicAttributes.html) action in the *Amazon Simple Notification Service API Reference*\.

## Amazon SQS permissions<a name="eb-sqs-permissions"></a>

To allow an EventBridge rule to invoke an Amazon SQS queue, use the `aws sqs get-queue-attributes` and `aws sqs set-queue-attributes` commands\.

If the policy for the SQS queue is empty, you first need to create a policy and then you can add the permissions statement to it\. A new SQS queue has an empty policy\.

If the SQS queue already has a policy, you need to copy the original policy and combine it with a new statement to add the permissions statement to it\.

**To add permissions that enable EventBridge rules to invoke an SQS queue**

1. To list SQS queue attributes\. At a command prompt, enter the following command\.

   ```
   aws sqs get-queue-attributes \
   --queue-url https://sqs.region.amazonaws.com/account-id/queue-name \
   --attribute-names Policy
   ```

1. Add the following statement\. \.

   ```
   {
     "Sid": "EventsToMyQueue",
     "Effect": "Allow",
     "Principal": {
        "Service": "events.amazonaws.com"
     },
     "Action": "sqs:SendMessage",
     "Resource": "arn:aws:sqs:region:account-id:queue-name",
     "Condition": {
       "ArnEquals": {
         "aws:SourceArn": "arn:aws:events:region:account-id:rule/rule-name"
       }
     }
   }
   ```

1. Use a [JSON to string converter](https://tools.knowledgewalls.com/jsontostring) to convert the preceding statement into a string\. After you convert the policy to a string, it looks like the following\.

   ```
   {\"Sid\": \"EventsToMyQueue\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"events.amazonaws.com\"}, \"Action\": \"sqs:SendMessage\", \"Resource\": \"arn:aws:sqs:region:account-id:queue-name\", \"Condition\": {\"ArnEquals\": {\"aws:SourceArn\": \"arn:aws:events:region:account-id:rule/rule-name\"}}
   ```

1. Create a file called `set-queue-attributes.json` with the following content\.

   ```
   {
       "Policy": "{\"Version\":\"2012-10-17\",\"Id\":\"arn:aws:sqs:region:account-id:queue-name/SQSDefaultPolicy\",\"Statement\":[{\"Sid\": \"EventsToMyQueue\", \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"events.amazonaws.com\"}, \"Action\": \"sqs:SendMessage\", \"Resource\": \"arn:aws:sqs:region:account-id:queue-name\", \"Condition\": {\"ArnEquals\": {\"aws:SourceArn\": \"arn:aws:events:region:account-id:rule/rule-name\"}}}]}"
   }
   ```

1. Set the policy attribute by using the `set-queue-attributes.json` file you just created as the input, as shown in the following command\.

   ```
   aws sqs set-queue-attributes \
   --queue-url https://sqs.region.amazonaws.com/account-id/queue-name \
   --attributes file://set-queue-attributes.json
   ```

For more information, see [Amazon SQS Policy Examples](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/SQSExamples.html) in the *Amazon Simple Queue Service Developer Guide*\.