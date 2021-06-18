# Tutorial: Create an Amazon EventBridge rule for AWS CloudTrail API calls<a name="eb-ct-api-tutorial"></a>

To create a [rule](eb-rules.md) that triggers on an action by an AWS service that doesn't generate [events](eb-events.md), you can match API calls made by that service instead\. AWS CloudTrail records API calls for some services\. For more information , see [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html)\.

Rules in EventBridge only work in the Region where they're created\. If you configure CloudTrail to track API calls in multiple Regions and you want a rule based on CloudTrail for each of those Regions, you must create a separate rule in each Region that you want to track\.

All events that are delivered by CloudTrail have `AWS API Call via CloudTrail` as the value for `detail-type`\. Events from API actions that start with the keywords `List`, `Get`, or `Describe` aren't processed by EventBridge, with the exception of events from the following AWS STS actions:
+ `GetFederationToken`
+ `GetSessionToken`

To record events with a `detail-type` value of `AWS API Call via CloudTrail`, a CloudTrail trail with logging enabled is required\. 

**Note**  
It is possible to create a rule that leads to an *infinite loop*, where a rule runs repeatedly\. To prevent infinite loops, rules must not run actions that match the same rule\.   
For example, a rule that detects that ACLs have changed on an S3 bucket and then runs software to change them to a new state causes the same rule to run again\.   
For example, your rule could fire only if ACLs are found to be in a bad state instead of after any change\.   
An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified quota\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html) in the *AWS Billing and Cost Management User Guide*\.

**Important**  
CloudTrail is extending event delivery through EventBridge to resource owners\. Currently, CloudTrail supports both API callers and resource owners to receive events in their S3 buckets by creating trails, and delivers events to API callers through EventBridge\. With this change, resource owners in addition to API callers will be able to monitor cross\-account API calls through EventBridge\. CloudTrail’s integration with EventBridge provides a convenient way to set automated rules\-based workflows in response to events\.  
The CloudTrail team began deploying this update on April 12, 2021 and expect to complete this update in all commercial regions early this month\. As a result, some customers might experience an increase in the EventBridge events being delivered through EventBridge coinciding with this update\. No customer action is required, but should you have any questions, please reach out to AWS Support\. 

**Topics**
+ [Step 1: Create a rule](#eb-ct-api-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a rule<a name="eb-ct-api-create-rule"></a>

Create a rule that triggers on an API call via CloudTrail

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Choose **Pre\-defined pattern by service**\.

1. For **Service provider**, choose **AWS**\.

1. For **Service name**, choose **CloudTrail**\.

1. For **Event type**, choose **AWS API Call via CloudTrail**\.

   If you want to customize the event pattern, choose **Edit**, make your changes, and choose **Save**\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Target**, select up to 5 targets to send **AWS API Call via CloudTrail** to\. Configure any additional settings for the selected target\. The additional settings vary by target\.

   For many target types, such as a **Step Functions state machine**, EventBridge needs permissions to send events to the target resource\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. For **Retry policy and dead\-letter queue:**, under **Retry policy**:

   1. For **Maximum age of event**, enter a value between 1 minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they aren't successfully delivered to the target\. Do one of the following:
   + Choose **None** to not use a dead\-letter queue\.
   + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
   + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. To learn more, see [Granting permissions to the dead\-letter queue](eb-rule-dlq.md#eb-dlq-perms)\.

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully completed this tutorial\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.