# Archive and replay Amazon EventBridge events<a name="eb-tutorial-archive-replay"></a>

You can use EventBridge to route [events](eb-events.md) to specific [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) functions using [rules](eb-rules.md)\.

In this tutorial, you’ll create a function to use as the target for the EventBridge rule using the Lambda console\. Then, you'll create an [archive](eb-archive-event.md) and a rule that'll archive test events using the EventBridge console\. Once there are events in that archive, you'll [replay](eb-replay-archived-event.md) them\. 

**Topics**
+ [Step 1: Create a Lambda function](#eb-create-lambda-function)
+ [Step 2: Create archive](#eb-ar-create-archive)
+ [Step 3: Create rule](#eb-ar-create-rule)
+ [Step 4: Send test events](#eb-ar-send-test-events)
+ [Step 5: Replay events](#eb-ar-replay-events)
+ [Step 6: Clean up your resources](#cleanup)

## Step 1: Create a Lambda function<a name="eb-create-lambda-function"></a>

First, create a Lambda function to log the events\.

**To create a Lambda function:**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Enter a name and description for the Lambda function\. For example, name the function `LogScheduledEvent`\.

1. Leave the rest of the options as the defaults and choose **Create function**\.

1. On the **Code** tab of the function page, double\-click **index\.js**\.

1. Replace the existing JavaScript code with the following code:

   ```
   'use strict';
   
   exports.handler = (event, context, callback) => {
       console.log('LogScheduledEvent');
       console.log('Received event:', JSON.stringify(event, null, 2));
       callback(null, 'Finished');
   };
   ```

1. Choose **Deploy**\.

## Step 2: Create archive<a name="eb-ar-create-archive"></a>

Next, create the archive that will hold all the test events\.

**To create an archive**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Archives**\.

1. Choose **Create archive**\.

1. Enter a name and description for the archive\. For example, name the archive `ArchiveTest`\.

1. Leave the rest of the options as the defaults and choose **Next**\.

1. Choose **Create archive**\.

## Step 3: Create rule<a name="eb-ar-create-rule"></a>

Create a rule to archive events that are sent to the event bus\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, name the rule `ARTestRule`\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Custom pattern** and paste the following code into the **Event pattern** field:

      ```
      {
        "detail-type": [
          "customerCreated"
        ]
      }
      ```

   1. Choose **Save**\.

1. For **Select event bus**, choose **AWS default event bus**\.

1. For **Target**, choose **Lambda function**\.

1. For **Function**, select the Lambda function that you created in the **Step 1: Create a Lambda function** section\. In this example, select `LogScheduledEvent`\.

1. Choose **Create**\.

## Step 4: Send test events<a name="eb-ar-send-test-events"></a>

Now that you've set up the archive and the rule, we'll send test events to make sure the archive is working correctly\.

**Note**  
It can take some time for events to get to the archive\.

**To send test events \(console\)**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. In the **Default event bus** tile, choose **Actions**, **Send events**\.

1. Enter an event source\. For example, `TestEvent`\.

1. For **Detail type**, enter `customerCreated`\.

1. For **Event detail**, enter `{}`\.

1. Choose **Send**\.

## Step 5: Replay events<a name="eb-ar-replay-events"></a>

Once the test events are in the archive you can replay them\.

**To replay archived events \(console\)**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Replays**\.

1. Choose **Start new replay**\.

1. Enter a name and description for the replay\. For example, name the replay `ReplayTest`\.

1. For **Source**, select the archive you created in the **Step 2: Create archive** section\.

1. For **Replay time frame**, do the following\.

   1.  For **Start time**, select the date you sent test events and a time before you sent them\. For example, `2021/08/11` and `08:00:00`\. 

   1.  For **End time**, select the current date and time\. For example, `2021/08/11` and `09:15:00`\. 

1. Choose **Start Replay**\.

## Step 6: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the Lambda function\(s\)**

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console\.

1. Select the function\(s\) that you created\.

1. Choose **Actions**, **Delete**\.

1. Choose **Delete**\.

**To delete the EventBridge archives\(s\)**

1. Open the [Archives page](https://console.aws.amazon.com/events/home#/archives) of the EventBridge console\.

1. Select the archive\(s\) you created\.

1. Choose **Delete**\.

1. Enter the archive name and choose **Delete**\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.