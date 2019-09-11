# Tutorial: Schedule Automated Amazon EBS Snapshots Using EventBridge<a name="take-scheduled-snapshot"></a>

You can run EventBridge rules according to a schedule\. In this tutorial, you create an automated snapshot of an existing Amazon Elastic Block Store \(Amazon EBS\) volume on a schedule\. You can choose a fixed rate to create a snapshot every few minutes or use a cron expression to specify that the snapshot is made at a specific time of day\.

**Important**  
Creating rules with built\-in targets is supported only on the AWS Management Console\.

## Step 1: Create a Rule<a name="ebs-create-rule"></a>

Create a rule that takes snapshots on a schedule\. You can use a rate expression or a cron expression to specify the schedule\. For more information, see [Schedule Expressions for Rules](scheduled-events.md)\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Fixed rate every** and specify the schedule interval \(for example, 5 minutes\)\. Alternatively, choose **Cron expression** and specify a cron expression \(for example, every 15 minutes Monday through Friday, starting at the current time\)\.

1. For **Select event bus**, choose **AWS default event bus**\. Scheduled rules are supported only on the default event bus\.

1. For **Target**, choose select **EC2 CreateSnapshot API call**\.

1. For **Volume ID**, enter the volume ID of the targeted Amazon EBS volume\.

1. Choose **Create a new role for this specific resource**\. The new role grants the target permissions to access resources on your behalf\.

1. Choose **Create**\.

## Step 2: Test the Rule<a name="ebs-test-rule"></a>

You can verify your rule by viewing your first snapshot after it's taken\.

**To test your rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Snapshots**\.

1. Verify that the first snapshot appears in the list\.

1. \(Optional\) When you're finished, disable the rule to prevent additional snapshots from being taken\.

   1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

   1. In the navigation pane, choose **Rules**\.

   1. Select the button next to the rule and choose **Disable**\.

   1. When prompted for confirmation, choose **Disable**\.