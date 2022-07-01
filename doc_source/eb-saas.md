# Receiving events from a SaaS partner with Amazon EventBridge<a name="eb-saas"></a>

To receive [events](eb-events.md) from SaaS partner applications and services, you need a partner event source from the partner\. Then you can create a partner [event bus](eb-event-bus.md) and match it to the partner event source\.



**Topics**
+ [Supported SaaS partner integrations](#eb-supported-integrations)
+ [Configuring Amazon EventBridge to receive events from a SaaS integration](#eb-saas-integration)
+ [Creating a rule that matches SaaS partner events](#eb-create-rule-partner-events)
+ [Receiving events from Salesforce](eb-saas-salesforce.md)

## Supported SaaS partner integrations<a name="eb-supported-integrations"></a>

EventBridge supports the following SaaS partner integrations:
+ [https://console.aws.amazon.com/events/#/partners/auth0.com?page=overview](https://console.aws.amazon.com/events/#/partners/auth0.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/blitline.com?page=overview](https://console.aws.amazon.com/events/#/partners/blitline.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/buidlhub.com?page=overview](https://console.aws.amazon.com/events/#/partners/buidlhub.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/buildkite.com?page=overview](https://console.aws.amazon.com/events/#/partners/buildkite.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/camunda.com?page=overview](https://console.aws.amazon.com/events/#/partners/camunda.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/clevertap.com?page=overview](https://console.aws.amazon.com/events/#/partners/clevertap.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/datadoghq.com?page=overview](https://console.aws.amazon.com/events/#/partners/datadoghq.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/epsagon.com?page=overview](https://console.aws.amazon.com/events/#/partners/epsagon.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/freshworks.com?page=overview](https://console.aws.amazon.com/events/#/partners/freshworks.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/genesys.com?page=overview](https://console.aws.amazon.com/events/#/partners/genesys.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/gs2.io?page=overview](https://console.aws.amazon.com/events/#/partners/gs2.io?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/karte.io?page=overview](https://console.aws.amazon.com/events/#/partners/karte.io?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/kloudless.com?page=overview](https://console.aws.amazon.com/events/#/partners/kloudless.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/mackerel.io?page=overview](https://console.aws.amazon.com/events/#/partners/mackerel.io?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/mongodb.com?page=overview](https://console.aws.amazon.com/events/#/partners/mongodb.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/newrelic.com?page=overview](https://console.aws.amazon.com/events/#/partners/newrelic.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/onelogin.com?page=overview](https://console.aws.amazon.com/events/#/partners/onelogin.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/opsgenie.com?page=overview](https://console.aws.amazon.com/events/#/partners/opsgenie.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/pagerduty.com?page=overview](https://console.aws.amazon.com/events/#/partners/pagerduty.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/payshield.com.au?page=overview](https://console.aws.amazon.com/events/#/partners/payshield.com.au?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/sailpoint.com?page=overview](https://console.aws.amazon.com/events/#/partners/sailpoint.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/saviynt.com?page=overview](https://console.aws.amazon.com/events/#/partners/saviynt.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/segment.com?page=overview](https://console.aws.amazon.com/events/#/partners/segment.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/shopify.com?page=overview](https://console.aws.amazon.com/events/#/partners/shopify.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/signalfx.com?page=overview](https://console.aws.amazon.com/events/#/partners/signalfx.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/site24x7.com?page=overview](https://console.aws.amazon.com/events/#/partners/site24x7.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/stax.io](https://console.aws.amazon.com/events/#/partners/stax.io)
+ [https://console.aws.amazon.com/events/#/partners/sugarcrm.com?page=overview](https://console.aws.amazon.com/events/#/partners/sugarcrm.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/symantec.com?page=overview](https://console.aws.amazon.com/events/#/partners/symantec.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/thundra.io?page=overview](https://console.aws.amazon.com/events/#/partners/thundra.io?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/triggermesh.com?page=overview](https://console.aws.amazon.com/events/#/partners/triggermesh.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/whispir.com?page=overview](https://console.aws.amazon.com/events/#/partners/whispir.com?page=overview)
+ [https://console.aws.amazon.com/events/#/partners/zendesk.com?page=overview](https://console.aws.amazon.com/events/#/partners/zendesk.com?page=overview)
+ [Amazon Seller Partner API](https://console.aws.amazon.com/events/#/partners/sellingpartnerapi.amazon.com?page=overview) 

Partner event sources are available in the following Regions\.


| Code | Name | 
| --- | --- | 
| `us-east-1` | US East \(N\. Virginia\) | 
| `us-east-2` | US East \(Ohio\) | 
| `us-west-1` | US West \(N\. California\) | 
| `us-west-2` | US West \(Oregon\) | 
| `ca-central-1` | Canada \(Central\) | 
| `eu-central-1` | Europe \(Frankfurt\) | 
| `eu-west-1` | Europe \(Ireland\) | 
| `eu-west-2` | Europe \(London\) | 
| `eu-west-3` | Europe \(Paris\) | 
| `eu-north-1` | Europe \(Stockholm\) | 
| `eu-south-1` | Europe \(Milan\) | 
| `af-south-1` | Africa \(Cape Town\) | 
| `ap-east-1` | Asia Pacific \(Hong Kong\) | 
| `ap-northeast-1` | Asia Pacific \(Tokyo\) | 
| `ap-northeast-2` | Asia Pacific \(Seoul\) | 
| `ap-northeast-3` | Asia Pacific \(Osaka\) | 
| `ap-southeast-1` | Asia Pacific \(Singapore\) | 
| `ap-southeast-2` | Asia Pacific \(Sydney\) | 
| `ap-southeast-3` | Asia Pacific \(Jakarta\) | 
| `cn-north-1` | China \(Beijing\) | 
| `cn-northwest-1` | China \(Ningxia\) | 
| `me-south-1` | Middle East \(Bahrain\) | 
| `sa-east-1` | South America \(São Paulo\) | 

## Configuring Amazon EventBridge to receive events from a SaaS integration<a name="eb-saas-integration"></a>

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Partner event sources**\.

1. Find the partner that you want and then choose **Set up** for that partner\.

1. To copy your account ID to the clipboard, choose **Copy**\.

1. In the navigation pane, choose **Partner event sources**\.

1. Go to the partner's website and follow the instructions to create a partner event source using your account ID\. The event source that you create is available to only your account\.

1. Go back to the EventBridge console and choose **Partner event sources** in the navigation pane\.

1. Select the button next to the partner event source and then choose **Associate with event bus**\. 

   The status of the event source changes from `Pending` to `Active`, and the name of the event bus updates to match the partner event source name\. You can now start creating rules that match events from the partner event source\. For more information, see [Creating a rule that matches SaaS partner events](#eb-create-rule-partner-events)\.

## Creating a rule that matches SaaS partner events<a name="eb-create-rule-partner-events"></a>

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select ** AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **Other**\.

1. \(Optional\) For **Sample events**, choose the type of event\.

1. For **Event pattern**, enter a JSON event pattern\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose the AWS service that you want to send information to when EventBridge detects an event that matches the event pattern\.

1. The fields displayed vary depending on the service you choose\. Enter information specific to this target type as needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run\. Do one of the following:
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created earlier, choose **Use existing role** and select the existing role from the drop\-down list\.

1. \(Optional\) For **Additional settings**, do the following:

   1. For **Maximum age of event**, enter a value between one minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
      + Choose **None** to not use a dead\-letter queue\.
      + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
      + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. For more information, see [Granting permissions to the dead\-letter queue](eb-rule-dlq.md#eb-dlq-perms)\.

1. \(Optional\) Choose **Add another target** to add another target for this rule\.

1. Choose **Next**\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.