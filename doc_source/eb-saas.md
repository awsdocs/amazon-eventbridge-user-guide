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
| `ap-southeast-1` | Asia Pacific \(Singapore\) | 
| `ap-southeast-2` | Asia Pacific \(Sydney\) | 
| `ap-south-1` | Asia Pacific \(Mumbai\) | 
| `sa-east-1` | South America \(São Paulo\) | 

## Configuring Amazon EventBridge to receive events from a SaaS integration<a name="eb-saas-integration"></a>

The following procedure describes how to configure EventBridge to receive [events](eb-events.md) from a SaaS integration partner\.

**To receive events from an SaaS partner**

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

Before you can create [rules](eb-rules.md) for [events](eb-events.md) from SaaS partner applications and services, you need a partner [event bus](eb-event-bus.md)\. Then you can match the partner event bus to the partner event source\. For more information, see [Receiving events from a SaaS partner with Amazon EventBridge](#eb-saas)\.

**To create a rule that matches an event from a SaaS partner**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

1. For **Define pattern**, choose **Event pattern**\.

1. Choose **Pre\-defined pattern by service**\.

1. For **Service provider**, choose **Service partners**\.

1. For **Service name**, choose the name of the partner\.

1. For **Event type**, choose **All Events** or choose the type of event to use for this rule\. If you choose **All Events**, all events that come from this partner event source match the rule\.

1. \(Optional\) To customize the event pattern, choose **Edit**, make your changes, and then choose **Save**\.

1. For **Service event bus**, select the event bus that corresponds to this partner\.

1. For **Select targets**, choose the AWS service that is to act when this type of event occurs\.

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, EventBridge needs permissions to send events to the target\. In these cases, EventBridge can create the IAM role needed for your rule to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**
   + To use an IAM role that you created before, choose **Use existing role**

1. \(Optional\) Choose **Add target** to add another target for this rule\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Create**\.