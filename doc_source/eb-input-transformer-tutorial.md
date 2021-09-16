# Tutorial: Use input transformer to customize what EventBridge passes to the event target<a name="eb-input-transformer-tutorial"></a>

You can use the [Input transformer](eb-transform-target-input.md) in EventBridge to customize text from an [event](eb-events.md) before you send it to the target of a [rule](eb-rules.md)\. 

To do this, you define JSON paths from the event and assign their outputs to different variables\. Then you can use those variables in the input template\. The characters < and > can't be escaped\. For more information, see [Transforming Amazon EventBridge target input](eb-transform-target-input.md)

**Note**  
If you specify a variable to match a JSON path that doesn't exist in the event, that variable isn't created and doesn't appear in the output\.

In this tutorial, you create a rule that runs when any instance changes state\. You extract the `instance-id` and `state` of an Amazon EC2 instance from the event\. Then you use the input transformer to put that data into the message that goes to an Amazon SNS topic\. The 

For example, the rule in this tutorial matches the following Amazon EC2 instance state\-change notification event\.

```
{
   "id":"7bf73129-1428-4cd3-a780-95db273d1602",
   "detail-type":"EC2 Instance State-change Notification",
   "source":"aws.ec2",
   "account":"123456789012",
   "time":"2015-11-11T21:29:54Z",
   "region":"us-east-1",
   "resources":[
      "arn:aws:ec2:us-east-1:123456789012:instance/ i-1234567890abcdef0"
   ],
   "detail":{
      "instance-id":" i-1234567890abcdef0",
      "state":"stopped"
   }
}
```

The input transformer maps the `instance` variable to the `$.detail.instance-id` JSON path from the event, and the `state` variable to the `$.detail.state` JSON path\. Then EventBridge puts the variables into the input template "The EC2 instance <instance> has changed state to <state>\." The result is the following Amazon SNS message\.

```
The EC2 instance i-1234567890abcdef0 has changed state to stopped.
```

**Topics**
+ [Step 1: Create a rule](#eb-input-transformer-create-rule)
+ [Step 2: Confirm success](#success)
+ [Step 3: Clean up your resources](#cleanup)

## Step 1: Create a rule<a name="eb-input-transformer-create-rule"></a>

Create a rule to use the input transformer to customize the instance state information that goes to a target\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Type a name and description for the rule\.

1. For **Define pattern**, do the following:

   1. Choose **Event pattern**\.

   1. Choose **Pre\-defined pattern by service**\.

   1. For **Service provider**, choose **AWS**\.

   1. For **Service Name**, choose **EC2**\.

   1. For **Event type**, choose **EC2 Instance State\-change Notification**\.

   1. Choose **Any state**, **Any instance**\.

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it goes to your account’s default event bus\. 

1. For **Target**, choose **SNS topic**\.

1. For **Topic**, select the Amazon SNS topic that is to be notified when Amazon EC2 instances change state\.

1. Choose **Configure input**, **Input Transformer**\.

1. For **Input Path**, enter **\{"state" : "$\.detail\.state", "instance" : "$\.detail\.instance\-id"\}**\.

1. For **Input Template**, enter **"The EC2 instance <instance> has changed state to <state>\."**

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