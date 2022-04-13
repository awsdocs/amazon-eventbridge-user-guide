# Tutorial: Schedule automated Amazon EBS snapshots using EventBridge<a name="eb-scheduled-snapshot"></a>

You can run EventBridge [rules](eb-rules.md) on a schedule\. In this tutorial, you create a snapshot of an existing [Amazon Elastic Block Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html) \(Amazon EBS\) volume on a schedule\. You can choose a fixed rate to create a snapshot every few minutes or use a cron expression to create the snapshot at a specific time of day\.

**Important**  
To create rules with built\-in [targets](eb-targets.md), you must use the AWS Management Console\.

**Topics**
+ [Step 1: Create the rule](#eb-ebs-create-rule)
+ [Step 2: Test the rule](#eb-ebs-test-rule)
+ [Step 3: Confirm success](#success)
+ [Step 4: Clean up your resources](#cleanup)

## Step 1: Create the rule<a name="eb-ebs-create-rule"></a>

Create a rule that takes snapshots on a schedule\. You can use a rate expression or a cron expression to specify the schedule\. For more information, see [Creating an Amazon EventBridge rule that runs on a schedule](eb-create-rule-schedule.md)\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Schedule**\.

1. Choose **Next**\.

1. For **Schedule pattern**, choose **A schedule that runs at a regular rate, such as every 10 minutes\.** and enter **5** and choose **Minutes** from the drop\-down list\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **EBS Create Snapshot** from the drop\-down list\.

1. For **Volume ID**, enter the volume ID of the Amazon EBS volume\.

1. For **Execution role**, choose **Create a new for role for this specific resource**\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 2: Test the rule<a name="eb-ebs-test-rule"></a>

You can verify your rule works by viewing your first snapshot after it's taken\.

**To test your rule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Snapshots**\.

1. Verify that the first snapshot appears in the list\.

## Step 3: Confirm success<a name="success"></a>

If you see the a snapshot in the list, you've successfully completed this tutorial\. If the snapshot isn't in the list, start troubleshooting by verifying the rule was created successfully\.

## Step 4: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.