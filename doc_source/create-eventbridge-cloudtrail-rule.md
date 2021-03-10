# Creating an EventBridge Rule That Triggers on an AWS API Call Using AWS CloudTrail<a name="create-eventbridge-cloudtrail-rule"></a>

To create a rule that triggers on an action by an AWS service that does not emit events, you can base the rule on API calls made by that service\. The API calls are recorded by AWS CloudTrail\. For more information about the API calls that you can use as triggers for rules, see [Services Supported by CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-supported-services.html)\.

Rules in EventBridge work only in the Region where they're created\. If you configure CloudTrail to track API calls in multiple Regions and you want a rule based on CloudTrail to trigger in each of those Regions, you must create a separate rule in each Region that you want to track\.

All events that are delivered via CloudTrail have `AWS API Call via CloudTrail` as the value for `detail-type`\. Events from API actions that start with the keywords `List`, `Get`, or `Describe` are not processed by EventBridge, with the exception of events from the following AWS STS actions:
+ GetFederationToken
+ GetSessionToken

**Note**  
You might accidentally create rules that lead to infinite loops, where a rule is fired repeatedly\. For example, a rule might detect that ACLs have changed on an S3 bucket and trigger software to change them to the desired state\. If you don't write the rule carefully, the subsequent change to the ACLs fires the rule again, creating an infinite loop\.  
To prevent this, write the rules so that the triggered actions don't refire the same rule\. For example, your rule could fire only if ACLs are found to be in a bad state instead of after any change\.   
An infinite loop can quickly cause higher than expected charges\. We recommend that you use budgeting, which alerts you when charges exceed your specified quota\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html) in the *AWS Billing and Cost Management User Guide*\.

**To create a rule that triggers on an API call via CloudTrail**

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

1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
   + Choose **None** to not use a dead\-letter queue\.
   + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
   + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. To learn more, see [Granting permissions to the DLQ](rule-dlq.md#dlq-perms)\.

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.