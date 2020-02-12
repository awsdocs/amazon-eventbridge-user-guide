# Creating an EventBridge Rule That Triggers on an AWS API Call Using AWS CloudTrail<a name="create-eventbridge-cloudtrail-rule"></a>

To create a rule that triggers on an action by an AWS service that does not emit events, you can base the rule on API calls made by that service\. The API calls are recorded by AWS CloudTrail\. For more information about the API calls that you can use as triggers for rules, see [Services Supported by CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-supported-services.html)\.

Rules in EventBridge work only in the Region where they're created\. If you configure CloudTrail to track API calls in multiple Regions and you want a rule based on CloudTrail to trigger in each of those Regions, you must create a separate rule in each Region that you want to track\.

All events that are delivered via CloudTrail have `AWS API Call via CloudTrail` as the value for `detail-type`\.

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

1. For **Service name**, choose the name of the service that emits the event\.

1. For **Event type**, choose **AWS API Call via CloudTrail**\.

   If you want to customize the event pattern, choose **Edit**, make your changes, and choose **Save**\.

1. For **Select event bus**, choose the event bus that you want to associate with this rule\.

1. For **Select event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to trigger on matching events that come from your own AWS account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. >

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Tagging Your Amazon EventBridge Resources](eventbridge-tagging.md)\.

1. Choose **Create**\.