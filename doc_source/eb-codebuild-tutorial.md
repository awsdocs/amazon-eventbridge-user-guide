# Tutorial: Schedule automated builds using AWS CodeBuild<a name="eb-codebuild-tutorial"></a>

In this tutorial, you schedule [CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) to run a build every week night at 20:00 UTC\. You also pass a constant to CodeBuild to be used for this scheduled build\. In this tutorial, we add the optional step of passing a parameter to CodeBuild, to override the default\. This isn't required when you set CodeBuild as the target\. 

For more information about the parameters that you can pass, see [StartBuild](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_StartBuild.html) in the *AWS CodeBuild API Reference*\. You can't pass the `projectName` parameter, but you can specify the project using the ARN in **Project ARN**\.

**Topics**
+ [Step 1: Create a Rule](#eb-codebuild-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a Rule<a name="eb-codebuild-create-rule"></a>

Create a rule to schedule a CodeBuild project build\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Cron expression** and specify the following as the expression: **0 20 ? \* MON\-FRI \*\.** \(for example, 5 minutes\)\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Targets**, choose **CodeBuild project**\.

1. For **Project ARN**, enter the ARN of the build project\.

1. \(Optional\) To pass a parameter to CodeBuild, do the following:

   1. Choose **Configure input** and then choose **Constant \(JSON text\)**\.

   1. In the box under **Constant \(JSON text\)**, enter the following to set the timeout override to 30 minutes for these scheduled builds: **\{ "timeoutInMinutesOverride": 30 \}**\.

1. Do one of the following:
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\. This must be a role that already has sufficient permissions to invoke the build\. EventBridge doesn't grant additional permissions to the role that you select\.

1. Choose **Create**\.

## Step 2: Confirm success<a name="success"></a>

If you see the rule in the list of rules, you've successfully created the rule\.

## Step 3: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.