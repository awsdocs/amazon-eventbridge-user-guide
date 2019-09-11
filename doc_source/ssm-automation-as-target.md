# Tutorial: Set AWS Systems Manager Automation as an EventBridge Target<a name="ssm-automation-as-target"></a>

You can use EventBridge to invoke AWS Systems Manager Automation on a regular timed schedule or when specified events are detected\. This tutorial assumes that you're invoking Systems Manager Automation based on certain events\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name** and **Event type**, choose the service and event type to use as the trigger\. Depending on the service and event type that you choose, you might need to specify additional options\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Target**, choose **SSM Automation\.** 

1. For **Document**, choose the Systems Manager document to run when the target is triggered\.

1. \(Optional\) To specify a certain version of the document, choose **Configure document version**\.

1. Under **Configure automation parameter\(s\)**, choose **No Parameter\(s\)** or **Constant**\.

   If you choose **Constant**, specify the constants to pass to the document execution\.

1. EventBridge can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. Choose **Create**\.