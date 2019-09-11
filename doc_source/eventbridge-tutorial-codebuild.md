# Tutorial: Schedule Automated Builds Using AWS CodeBuild<a name="eventbridge-tutorial-codebuild"></a>

In the example in this tutorial, you schedule CodeBuild to run a build every week night at 20:00 GMT\. You also pass a constant to CodeBuild to be used for this scheduled build\.

**To create a rule scheduling a CodeBuild project build nightly at 20:00 GMT**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Cron expression** and specify the following as the expression: **0 20 ? \* MON\-FRI \*\.** \(for example, 5 minutes\)\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Targets**, choose **CodeBuild project**\.

1. For **Project ARN**, enter the ARN of the build project\.

1. In this tutorial, we add the optional step of passing a parameter to CodeBuild, to override the default\. This isn't required when you set CodeBuild as the target\. To pass the parameter, choose **Configure input**, **Constant \(JSON text\)**\.

   In the box under **Constant \(JSON text\)**, enter the following to set the timeout override to 30 minutes for these scheduled builds: **\{ "timeoutInMinutesOverride": 30 \}**\.

   For more information about the parameters that you can pass, see [StartBuild](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_StartBuild.html) in the *AWS CodeBuild API Reference*\. You can't pass the `projectName` parameter in this field\. Instead, specify the project using the ARN in **Project ARN**\.

1. EventBridge can create the IAM role needed for your build project to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\. This must be a role that already has sufficient permissions to invoke the build\. EventBridge doesn't grant additional permissions to the role that you select\.

1. Choose **Create**\.