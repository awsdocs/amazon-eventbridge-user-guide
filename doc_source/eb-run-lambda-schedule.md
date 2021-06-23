# Tutorial: Schedule AWS Lambda functions using EventBridge<a name="eb-run-lambda-schedule"></a>

You can set up a [rule](eb-rules.md) to run an [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function on a schedule\. This tutorial shows how to use the AWS Management Console or the AWS CLI to create the rule\. If you want to use the AWS CLI but haven't installed it, see the [Installing, updating, and uninstalling the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)\.

For schedules, EventBridge doesn't provide second\-level precision in [schedule expressions](eb-create-rule-schedule.md)\. The finest resolution using a cron expression is one minute\. Due to the distributed nature of EventBridge and the target services, there can be a delay of several seconds between the time the scheduled rule is triggered and the time the target service runs the target resource\. 

**Topics**
+ [Step 1: Create an AWS Lambda function](#eb-create-lambda-function)
+ [Step 2: Create a Rule](#eb-schedule-create-rule)
+ [Step 3: Verify the rule](#eb-schedule-test-rule)
+ [Step 4: Confirm success](#success)
+ [Step 5: Clean up your resources](#cleanup)

## Step 1: Create an AWS Lambda function<a name="eb-create-lambda-function"></a>

Create a Lambda function to log the scheduled events\. 

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name and description for the Lambda function\. For example, name the function `LogScheduledEvent`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing code with the following code\.

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogScheduledEvent');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 2: Create a Rule<a name="eb-schedule-create-rule"></a>

Create a rule to run the Lambda function you created in step 1 on a schedule\.

You can use either the console or the AWS CLI to create the rule\. To use the AWS CLI, you first grant the rule permission to invoke your Lambda function\. Then you can create the rule and add the Lambda function as a target\.

**To create a rule \(console\)**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Schedule**\.

   1. Choose **Fixed rate every** and specify the schedule interval, for example, 5 minutes\.

1. For **Select event bus**, choose **AWS default event bus**\. Scheduled rules are supported only on the default event bus\.

1. For **Target**, choose **Lambda function**\.

1. For **Function**, select the Lambda function that you created in step 1\.

1. Choose **Create**\.

**To create a rule \(AWS CLI\)**

1. To create a rule that runs on a schedule, use the  `put-rule` command\.

   ```
   aws events put-rule \
   --name my-scheduled-rule \
   --schedule-expression 'rate(5 minutes)'
   ```

   When this rule runs, it creates an event and then sends it to the targets\. The following is an example event\.

   ```
   {
       "version": "0",
       "id": "53dc4d37-cffa-4f76-80c9-8b7d4a4d2eaa",
       "detail-type": "Scheduled Event",
       "source": "aws.events",
       "account": "123456789012",
       "time": "2015-10-08T16:53:06Z",
       "region": "us-east-1",
       "resources": [
           "arn:aws:events:us-east-1:123456789012:rule/my-scheduled-rule"
       ],
       "detail": {}
   }
   ```

1. To grant the EventBridge service principal \(`events.amazonaws.com`\) permission to run the rule, use the  `add-permission` command\.

   ```
   aws lambda add-permission \
   --function-name LogScheduledEvent \
   --statement-id my-scheduled-event \
   --action 'lambda:InvokeFunction' \
   --principal events.amazonaws.com \
   --source-arn arn:aws:events:us-east-1:123456789012:rule/my-scheduled-rule
   ```

1. To add the Lambda function that you created in step 1 to the rule, use the  `put-targets`command \.

   ```
   aws events put-targets --rule my-scheduled-rule --targets file://targets.json
   ```

1. Create the file `targets.json` with the following contents\.

   ```
   [
     {
       "Id": "1", 
       "Arn": "arn:aws:lambda:us-east-1:123456789012:function:LogScheduledEvent"
     }
   ]
   ```

## Step 3: Verify the rule<a name="eb-schedule-test-rule"></a>

Wait at least five minutes after completing step 2, and then you can verify that your Lambda function was invoked\.

**To test your rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose the name of the rule that you created in step 2, and then choose **Metrics for the rule**\.

1. To view the output from your Lambda function, do the following:

   1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

   1. In the navigation pane, choose **Logs**\.

   1. Select the name of the log group for your Lambda function \(`/aws/lambda/function-name`\)\.

   1. Select the name of the log stream to view the data provided by the function for the instance that you launched\. 

## Step 4: Confirm success<a name="success"></a>

If you see the Lambda event in the CloudWatch logs, you've successfully completed this tutorial\. If the event isn't in your CloudWatch logs, start troubleshooting by verifying the rule was created successfully and, if the rule looks correct, verify the code of your Lambda function is correct\.

## Step 5: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.

**To delete the Lambda function**

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console\.

1. Select the function that you created\.

1. Choose **Actions**, **Delete**\.

1. Choose **Delete**\.
