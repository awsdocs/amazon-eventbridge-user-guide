# Tutorial: Use Input Transformer to Customize What Is Passed to the Event Target<a name="eventbridge-input-transformer-tutorial"></a>

You can use the input transformer feature of EventBridge to customize the text that is taken from an event before it's input to the target of a rule\. 

You can define multiple JSON paths from the event and assign their outputs to different variables\. Then you can use those variables in the input template as <*variable\-name*>\. The characters < and > cannot be escaped\.

If you specify a variable to match a JSON path that doesn't exist in the event, that variable isn't created and doesn't appear in the output\.

In this tutorial, we extract the `instance-id` and `state` of an Amazon EC2 instance from the instance state change event\. We use the input transformer to put that data into an easy\-to\-read message that is sent to an Amazon SNS topic\. The rule is triggered when any instance changes to any state\. For example, with this rule, the following Amazon EC2 instance state\-change notification event produces the Amazon SNS message **The EC2 instance i\-1234567890abcdef0 has changed state to stopped\.**

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

We achieve this by mapping the `instance` variable to the `$.detail.instance-id` JSON path from the event, and the `state` variable to the `$.detail.state` JSON path\. We then set the input template as "The EC2 instance <instance> has changed state to <state>\."

**Note**  
For more information on the event transformer, see [Transforming Target Input](transform-input.md)

## Create a Rule<a name="input-transformer-create-rule"></a>

**To customize instance state change information sent to a target using the input transformer**

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

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\. 

1. For **Target**, choose **SNS topic**\.

1. For **Topic**, select the Amazon SNS topic that is to be notified when Amazon EC2 instances change state\.

1. Choose **Configure input**, **Input Transformer**\.

1. For **Input Path**, enter **\{"state" : "$\.detail\.state", "instance" : "$\.detail\.instance\-id"\}**\.

1. For **Input Template**, enter **"The EC2 instance <instance> has changed state to <state>\."**

1. Choose **Create**\.