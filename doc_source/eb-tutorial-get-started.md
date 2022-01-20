# Create an Amazon EventBridge sample application<a name="eb-tutorial-get-started"></a>

You can use EventBridge to route [events](eb-events.md) to specific Lambda functions using [rules](eb-rules.md)\.

In this tutorial, you’ll use the AWS CLI, Node\.js, and the code in the [ GitHub repo](https://github.com/aws-samples/amazon-eventbridge-producer-consumer-example) to create the following:
+ An [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) function that produces events for bank ATM transactions\.
+ Three Lambda functions to use as [targets](eb-targets.md) of an EventBridge rule\.
+ and the rule that routes the created events to the correct downstream function based on an [event pattern](eb-event-patterns.md)\.

This example uses AWS SAM templates to define the EventBridge rules\. To learn more about using AWS SAM templates with EventBridge see [Using Amazon EventBridge and AWS Serverless Application Model templates](eb-use-sam.md)\.

In the repo, the *atmProducer* subdirectory contains `handler.js`, which represents the ATM service producing events\. This code is a Lambda handler written in Node\.js, and publishes events to EventBridge via the [AWS SDK](https://www.npmjs.com/package/aws-sdk) using this line of JavaScript code\.

```
const result = await eventbridge.putEvents(params).promise()
```

This directory also contains `events.js`, listing several test transactions in an Entries array\. A single event is defined in JavaScript as follows:

```
{
  // Event envelope fields
  Source: 'custom.myATMapp',
  EventBusName: 'default',
  DetailType: 'transaction',
  Time: new Date(),

  // Main event body
  Detail: JSON.stringify({
    action: 'withdrawal',
    location: 'MA-BOS-01',
    amount: 300,
    result: 'approved',
    transactionId: '123456',
    cardPresent: true,
    partnerBank: 'Example Bank',
    remainingFunds: 722.34
  })
}
```

The *Detail* section of the event specifies transaction attributes\. These include the location of the ATM, the amount, the partner bank, and the result of the transaction\.

The `handler.js` file in the *atmConsumer* subdirectory contains three functions:

```
exports.case1Handler = async (event) => {
  console.log('--- Approved transactions ---')
  console.log(JSON.stringify(event, null, 2))
}

exports.case2Handler = async (event) => {
  console.log('--- NY location transactions ---')
  console.log(JSON.stringify(event, null, 2))
}

exports.case3Handler = async (event) => {
  console.log('--- Unapproved transactions ---')
  console.log(JSON.stringify(event, null, 2))
}
```

Each function receives transaction events, which are logged via the `console.log` statements to [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html)\. The consumer functions operate independently of the producer and are unaware of the source of the events\.

The routing logic is contained in the EventBridge rules that are deployed by the application’s AWS SAM template\. The rules evaluate the incoming stream of events, and route matching events to the target Lambda functions\.

The rules use event patterns that are JSON objects with the same structure as the events they match\. Here's the event pattern for the one of the rules\.

```
{
  "detail-type": ["transaction"],
  "source": ["custom.myATMapp"],
  "detail": {
    "location": [{
      "prefix": "NY-"
    }]
  }
}
```

**Topics**
+ [Prerequisites](#eb-gs-prereqs)
+ [Step 1: Create application](#eb-gs-create-application)
+ [Step 2: Run application](#eb-gs-run-application)
+ [Step 3: Check the logs and verify the application works](#eb-gs-check-logs)
+ [Step 4: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-gs-prereqs"></a>

To complete this tutorial, you'll need the following resources:
+ An AWS account\. [Create an AWS account](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) if you don't already have one\.
+ AWS CLI installed\. To install the AWS CLI, see the [Installing, updating, and uninstalling the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)\.
+ Node\.js 12\.x installed\. To install Node\.js, see [ Downloads](https://nodejs.org/en/download/)\.

## Step 1: Create application<a name="eb-gs-create-application"></a>

To set up the example application, you'll use the AWS CLI and Git to create the AWS resources you'll need\.

**To create the application**

1. [Sign in to AWS](https://console.aws.amazon.com/console/home)\.

1. [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [install the AWS Serverless Application Model CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html) on your local machine\.

1. Create a new directory, and then navigate to that directory in a terminal\.

1. At the command line, enter `git clone https://github.com/aws-samples/amazon-eventbridge-producer-consumer-example`\.

1. At the command line run the following command:

   ```
   cd ./amazon-eventbridge-producer-consumer-example
   sam deploy --guided
   ```

1. In the terminal, do the following:

   1. For `Stack Name`, enter a name for the stack\. For example, name the stack `Test`\.

   1. For `AWS Region`, enter the Region\. For example, `us-west-2`\.

   1. For `Confirm changes before deploy`, enter `Y`\.

   1. For `Allow SAM CLI IAM role creation`, enter `Y`

   1. For `Save arguments to configuration file`, enter `Y`

   1. For `SAM configuration file`, enter `samconfig.toml`\.

   1. For `SAM configuration environment`, enter `default`\.

## Step 2: Run application<a name="eb-gs-run-application"></a>

Now that you've set up the resources, you'll use the console to test the functions\.

**To run the application**

1. Open the [Lambda console](https://console.aws.amazon.com/lambda/) in the same Region where you deployed the AWS SAM application\.

1. There are four Lambda functions with the prefix **atm\-demo**\. Select the **atmProducerFn** function, then choose **Actions**, **Test**\.

1. Enter `Test` for the **Name**\.

1. Choose **Test**\.

## Step 3: Check the logs and verify the application works<a name="eb-gs-check-logs"></a>

Now that you've run the application, you'll use the console to check the CloudWatch Logs\.

**To check the logs**

1. Open the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/) in the same Region where you ran the AWS SAM application\.

1. Choose **Logs**, and then choose **Log groups**\.

1. Select the log group containing **atmConsumerCase1**\. You see two streams representing the two transactions approved by the ATM\. Choose a log stream to view the output\.

1. Navigate back to the list of log groups, and then select the log group containing **atmConsumerCase2**\. You'll see two streams representing the two transactions matching the *New York* location filter\.

1. Navigate back to the list of log groups, and select the log group containing **atmConsumerCase3**\. Open the stream to see the denied transactions\.

## Step 4: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.

**To delete the Lambda function\(s\)**

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console\.

1. Select the function\(s\) that you created\.

1. Choose **Actions**, **Delete**\.

1. Choose **Delete**\.

**To delete the CloudWatch Logs log group\(s\)**

1. Open the [Cloudwatch console](https://console.aws.amazon.com/Cloudwatch/home)\.

1. Choose **Logs**, **Log groups**\.

1. Select the log group\(s\) that were created in this tutorial\.

1. Choose **Actions**, **Delete log group\(s\)**\.

1. Choose **Delete**\.