# Receiving events from a SaaS partner<a name="create-partner-event-bus"></a>

To be able to receive events from SaaS partner applications and services, you must have a partner event source offered to you from the partner\. You can then create a partner event bus and match it to the corresponding partner event source\.

## Supported SaaS partner integrations<a name="eb-supported-integrations"></a>

EventBridge supports the following SaaS partner integrations:
+ [Auth0](https://console.aws.amazon.com/events/#/partners/auth0.com?page=overview)
+ [Blitline](https://console.aws.amazon.com/events/#/partners/blitline.com?page=overview)
+ [BUIDLHub](https://console.aws.amazon.com/events/#/partners/buidlhub.com?page=overview)
+ [Buildkite](https://console.aws.amazon.com/events/#/partners/buildkite.com?page=overview)
+ [Camunda](https://console.aws.amazon.com/events/#/partners/camunda.com?page=overview)
+ [CleverTap](https://console.aws.amazon.com/events/#/partners/clevertap.com?page=overview)
+ [Datadog](https://console.aws.amazon.com/events/#/partners/datadoghq.com?page=overview)
+ [Epsagon](https://console.aws.amazon.com/events/#/partners/epsagon.com?page=overview)
+ [Freshworks](https://console.aws.amazon.com/events/#/partners/freshworks.com?page=overview)
+ [GS2](https://console.aws.amazon.com/events/#/partners/gs2.io?page=overview)
+ [Genesys](https://console.aws.amazon.com/events/#/partners/genesys.com?page=overview)
+ [Karte](https://console.aws.amazon.com/events/#/partners/karte.io?page=overview)
+ [Kloudless](https://console.aws.amazon.com/events/#/partners/kloudless.com?page=overview)
+ [Mackerel](https://console.aws.amazon.com/events/#/partners/mackerel.io?page=overview)
+ [MongoDB](https://console.aws.amazon.com/events/#/partners/mongodb.com?page=overview)
+ [New Relic](https://console.aws.amazon.com/events/#/partners/newrelic.com?page=overview)
+ [OneLogin](https://console.aws.amazon.com/events/#/partners/onelogin.com?page=overview)
+ [Opsgenie](https://console.aws.amazon.com/events/#/partners/opsgenie.com?page=overview)
+ [PagerDuty](https://console.aws.amazon.com/events/#/partners/pagerduty.com?page=overview)
+ [Payshield](https://console.aws.amazon.com/events/#/partners/payshield.com.au?page=overview)
+ [SailPoint](https://console.aws.amazon.com/events/#/partners/sailpoint.com?page=overview)
+ [Saviynt](https://console.aws.amazon.com/events/#/partners/saviynt.com?page=overview)
+ [Segment](https://console.aws.amazon.com/events/#/partners/segment.com?page=overview)
+ [Shopify](https://console.aws.amazon.com/events/#/partners/shopify.com?page=overview)
+ [SignalFx](https://console.aws.amazon.com/events/#/partners/signalfx.com?page=overview)
+ [Site24x7](https://console.aws.amazon.com/events/#/partners/site24x7.com?page=overview)
+ [Stax](https://console.aws.amazon.com/events/#/partners/stax.io)
+ [SugarCRM](https://console.aws.amazon.com/events/#/partners/sugarcrm.com?page=overview)
+ [Symantec](https://console.aws.amazon.com/events/#/partners/symantec.com?page=overview)
+ [Thundra](https://console.aws.amazon.com/events/#/partners/thundra.io?page=overview)
+ [TriggerMesh](https://console.aws.amazon.com/events/#/partners/triggermesh.com?page=overview)
+ [Whispir](https://console.aws.amazon.com/events/#/partners/whispir.com?page=overview)
+ [Zendesk](https://console.aws.amazon.com/events/#/partners/zendesk.com?page=overview)
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