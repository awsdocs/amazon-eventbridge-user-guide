# API destination partners<a name="eb-api-destination-partners"></a>

Use the information provided by the following AWS Partners to configure an API destination and connection for their service or application\.

## Coralogix<a name="eb-api-destination-coralogix"></a>

**API destination invocation endpoint URL**  
For a full list of endpoints, see [Coralogix API Reference](https://coralogix.com/docs/log-query-simply-retrieve-data/)\.

**Supported authorization types**   
API Key

**Additional authorization parameters required**  
Header `"x-amz-event-bridge-access-key"`, the value is the Coralogix API Key

**Coralogix documentation**  
[Amazon EventBridge authentication](https://coralogix.com/docs/amazon-eventbridge/)

**Commonly used API operations**  
USA1 POST htttps://aws\-events\.coralogix\.us  
APAC1 POST htttps://aws\-events\.coralogix\.in  
APAC2 POST htttps://aws\-events\.coralogixsg\.com  
EUROPE1 POST htttps://aws\-events\.coralogix\.com  
EUROPE2 POST htttps://aws\-events\.eu2\.coralogix\.com

**Additional information**  
The events are stored as log entries with `applicationName=[AWS Account]` and `subsystemName=[event.source]`\.

## Datadog<a name="eb-api-destination-datadog"></a>

**API destination invocation endpoint URL**  
For a full list of endpoints, see [Datadog API Reference](https://docs.datadoghq.com/api/latest/)\.

**Supported authorization types**   
API Key

**Additional authorization parameters required**  
None

**Datadog documentation**  
[Authentication](https://docs.datadoghq.com/api/latest/authentication/)

**Commonly used API operations**  
POST https://api\.datadoghq\.com/api/v1/events  
POST https://http\-intake\.logs\.datadoghq\.com/v1/input

**Additional information**  
Endpoint URLs differ depending on the location of your Datadog organization\. For the correct URL for your organization, see [documentation](https://docs.datadoghq.com/api/latest/)\.

## Freshworks<a name="eb-api-destination-freshworks"></a>

**API destination invocation endpoint URL**  
For a list of endpoints, see [https://developers\.freshworks\.com/documentation/](https://developers.freshworks.com/documentation/)

**Supported authorization types**   
Basic, API Key

**Additional authorization parameters required**  
Not applicable

**Freshworks documentation**  
[Authentication](https://developers.freshdesk.com/api/#authentication)

**Commonly used API operations**  
https://developers\.freshdesk\.com/api/\#create\_ticket  
https://developers\.freshdesk\.com/api/\#update\_ticket  
https://developer\.freshsales\.io/api/\#create\_lead  
https://developer\.freshsales\.io/api/\#update\_lead

**Additional information**  
None

## MongoDB<a name="eb-api-destination-mongodb"></a>

**API destination invocation endpoint URL**  
https://webhooks\.mongodb\-realm\.com/api/client/v2\.0/app/*Realm App ID*/service/*HTTP Service Name*/incoming\_webhook/*Webhook Name*

**Supported authorization types**   
API Key

**Additional authorization parameters required**  
None

**MongoDB documentation**  
[API Key](https://docs.mongodb.com/realm/services/configure/service-webhooks/)  
[Authentication Providers](https://docs.mongodb.com/realm/authentication/providers/)

**Commonly used API operations**  
POST https://webhooks\.mongodb\-realm\.com/api/client/v2\.0/app/*Realm App ID*/service/*HTTP Service Name*/incoming\_webhook/*Webhook Name*

**Additional information**  
[EventbridgeAtlas](https://github.com/mongodb-developer/EventbridgeAtlas) demonstrates how you can use Realm Webhooks to perform CRUD operations on collections in MongoDB Atlas from EventBridge API endpoints\.

## New Relic<a name="api-destination-new-relic"></a>

**API destination invocation endpoint URL**  
For more information, see [Our EU and US region data centers](https://docs.newrelic.com/docs/using-new-relic/welcome-new-relic/get-started/our-eu-us-region-data-centers/)\.  
**Events**  
**US–** https://insights\-collector\.newrelic\.com/v1/accounts/*YOUR\_NEW\_RELIC\_ACCOUNT\_ID*/events  
**EU–** https://insights\-collector\.eu01\.nr\-data\.net/v1/accounts/*YOUR\_NEW\_RELIC\_ACCOUNT\_ID*/events  
**Metrics**  
**US–** https://metric\-api\.newrelic\.com/metric/v1  
**EU–** https://metric\-api\.eu\.newrelic\.com/metric/v1  
**Logs**  
**US–** https://log\-api\.newrelic\.com/log/v1  
**EU–** https://log\-api\.eu\.newrelic\.com/log/v1  
**Traces**  
**US–** https://trace\-api\.newrelic\.com/trace/v1  
**EU–** https://trace\-api\.eu\.newrelic\.com/trace/v1

**Supported authorization types**   
API Key

**New Relic documentation**  
[Metric API](https://docs.newrelic.com/docs/telemetry-data-platform/ingest-manage-data/ingest-apis/report-metrics-metric-api/)  
[Event API](https://docs.newrelic.com/docs/telemetry-data-platform/ingest-manage-data/ingest-apis/introduction-event-api/)  
[Log API](https://docs.newrelic.com/docs/logs/log-management/log-api/introduction-log-api/)  
[Trace API](https://docs.newrelic.com/docs/understand-dependencies/distributed-tracing/trace-api/introduction-trace-api/)

**Commonly used API operations**  
[Metric API](https://docs.newrelic.com/docs/telemetry-data-platform/ingest-manage-data/ingest-apis/report-metrics-metric-api/)  
[Event API](https://docs.newrelic.com/docs/telemetry-data-platform/ingest-manage-data/ingest-apis/introduction-event-api/)  
[Log API](https://docs.newrelic.com/docs/logs/log-management/log-api/introduction-log-api/)  
[Trace API](https://docs.newrelic.com/docs/understand-dependencies/distributed-tracing/trace-api/introduction-trace-api/)

**Additional information**  
[Metric API limits](https://docs.newrelic.com/docs/telemetry-data-platform/get-data/apis/metric-api-limits-restricted-attributes/)  
[Event API limits](https://docs.newrelic.com/docs/telemetry-data-platform/ingest-manage-data/ingest-apis/introduction-event-api/#limits)  
[Log API limits](https://docs.newrelic.com/docs/logs/log-management/log-api/introduction-log-api/#limits)  
[Trace API limits](https://docs.newrelic.com/docs/understand-dependencies/distributed-tracing/trace-api/trace-api-general-requirements-limits/)

## Salesforce<a name="api-destination-salesforce"></a>

**API destination invocation endpoint URL**  
**Sobject–** https:// *myDomainName*\.my\.salesforce\.com/services/data/*versionNumber*/sobjects /*SobjectEndpoint*/\*  
**Custom platform events–** https://*myDomainName*\.my\.salesforce\.com/services/data /*versionNumber*/sobjects/*customPlatformEndpoint*/\*  
For a full list of endpoints, see [Salesforce API Reference](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_list.htm)

**Supported authorization types**   
OAuth client credentials  
OAUTH tokens are refreshed when a 401 or 407 response is returned\.

**Additional authorization parameters required**  
[Salesforce Connected App](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_oauth_and_connected_apps.htm) Client Id and Client Secret\.  
One of the following authorization endpoints:  
+ **Production–** https://*MyDomainName*\.my\.salesforce\.com\./services/oauth2/token
+ **Sandbox without enhanced domains–** https://*MyDomainName*\-\-* SandboxName*\.my\. salesforce\.com/services /oauth2/token
+ **Sandbox with enhanced domains–** https://*MyDomainName*\-\-* SandboxName*\.sandbox\.my\.salesforce\.com/services/oauth2/token
The following key/value pairs:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-api-destination-partners.html)

**Salesforce documentation**  
[REST API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_what_is_rest_api.htm)

**Commonly used API operations**  
[Working with Object Metadata](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/using_resources_working_with_object_metadata.htm)  
[Working with Records](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/using_resources_working_with_records.htm)

**Additional information**  
For a tutorial explaining how to use the EventBridge console to create a connection to Salesforce, an API Destination, and a rule to route information to Salesforce, see [Tutorial: Create a connection to Salesforce as an API destination](eb-tutorial-salesforce.md)\.

## Slack<a name="eb-api-destination-slack"></a>

**API destination invocation endpoint URL**  
For a list of endpoints and other resources, see [Using the Slack Web API](https://api.slack.com/web)

**Supported authorization types**   
OAuth 2\.0  
OAUTH tokens are refreshed when a 401 or 407 response is returned\.  
When you create a Slack application and install it to your workspace, an OAuth bearer token will be created on your behalf to be used for authenticating calls by your API destination connection\.

**Additional authorization parameters required**  
Not applicable

**Slack documentation**  
[Basic app setup](https://api.slack.com/authentication/basics)  
[Installing with OAuth](https://api.slack.com/authentication/oauth-v2)  
[Retrieving messages](https://api.slack.com/messaging/retrieving)  
[Sending messages](https://api.slack.com/messaging/sending)  
[Sending messages using Incoming Webhooks](https://api.slack.com/messaging/webhooks)

**Commonly used API operations**  
https://slack\.com/api/chat\.postMessage

**Additional information**  
When configuring your EventBridge rule there are two configurations to highlight:  
+ Include a header parameter that defines the content type as “application/json;charset=utf\-8”\.
+ Use an input transformer to map the input event to the expected output for the Slack API, namely ensure that the payload sent to the Slack API has “channel” and “text” key/value pairs\.

## Shopify<a name="eb-api-destination-shopify"></a>

**API destination invocation endpoint URL**  
For a list of endpoints and other resouces and methods, see [Endpoints and requests](https://shopify.dev/api/admin-rest#endpoints)

**Supported authorization types**   
OAuth, API Key  
OAUTH tokens are refreshed when a 401 or 407 response is returned\.

**Additional authorization parameters required**  
Not applicable

**Shopify documentation**  
[Authentication and authorization overview](https://shopify.dev/apps/auth)

**Commonly used API operations**  
POST \- /admin/api/2022\-01/products\.json  
GET \- admin/api/2022\-01/products/\{product\_id\}\.json  
PUT \- admin/api/2022\-01/products/\{product\_id\}\.json  
DELETE \- admin/api/2022\-01/products/\{product\_id\}\.json

**Additional information**  
[Create an app](https://shopify.dev/apps/getting-started/create)  
[Amazon EventBridge webhook delivery](https://shopify.dev/apps/webhooks/configuration/eventbridge)  
[Access tokens for custom apps in the Shopify admin](https://shopify.dev/apps/auth/admin-app-access-tokens)  
[Product](https://shopify.dev/api/admin-rest/2021-10/resources/product#top)  
[Shopify Admin API](https://shopify.dev/api/admin)

## Splunk<a name="api-destination-splunk"></a>

**API destination invocation endpoint URL**  
https://*SPLUNK\_HEC\_ENDPOINT*:*optional\_port*/services/collector/raw

**Supported authorization types**   
Basic, API Key

**Additional authorization parameters required**  
None

**Splunk documentation**  
For both authorization types, you need an HEC token ID\. For more information, see [Set up and use HTTP Event Collector in Splunk Web](https://docs.splunk.com/Documentation/Splunk/8.1.2/Data/UsetheHTTPEventCollector)\.

**Commonly used API operations**  
POST https://*SPLUNK\_HEC\_ENDPOINT*:*optional\_port*/services/collector/raw

**Additional information**  
API Key – When configuring the endpoint for EventBridge, the API key name is “Authorization” and value is the Splunk HEC token ID\.  
Basic \(Username/Password\) – When configuring the endpoint for EventBridge, the username is “Splunk” and the password is the Splunk HEC token ID\.

## Sumo Logic<a name="api-destination-sumologic"></a>

**API destination invocation endpoint URL**  
HTTP Log and Metric Source endpoint URLs will be different for every user\. For more information, see [ HTTP Logs and Metrics Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source)\.

**Supported authorization types**   
Sumo Logic doesn’t require authentication on their HTTP Sources because there’s a unique key baked into the URL\. For this reason, you should make sure to treat that URL as a secret\.  
When you configure the EventBridge API destination, an authorization type is required\. To meet this requirement, select API Key and give it a key name of “dummy\-key” and a key value of “dummy\-value”\.

**Additional authorization parameters required**  
Not applicable

**Sumo Logic documentation**  
Sumo Logic has already built hosted sources to collect logs and metrics from many AWS services and you can use the information on their website to work with those sources\. For more information, see [Amazon Web Services](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/Amazon-Web-Services)\.  
If you’re generating custom events from an application and want to send them to Sumo Logic as either logs or metrics, then use EventBridge API Destinations and Sumo Logic HTTP Log and Metric Source endpoints\.  
+ To sign up and create a free Sumo Logic instance, see [Start your free trial today](https://www.sumologic.com/sign-up/)\.
+ For more information about using Sumo Logic, see [ HTTP Logs and Metrics Source](https://help.sumologic.com/03Send-Data/Sources/02Sources-for-Hosted-Collectors/HTTP-Source)\.

**Commonly used API operations**  
POST https://endpoint4\.collection\.us2\.sumologic\.com/receiver/v1/http/*UNIQUE\_ID\_PER\_COLLECTOR*

**Additional information**  
None

## TriggerMesh<a name="eb-api-destination-triggermesh"></a>

**API destination invocation endpoint URL**  
Use the information in the [Event Source for HTTP](https://docs.triggermesh.io/concepts/sources) topic to formulate the endpoint URL\. An endpoint URL includes the event source name and user namespace in the following format:  
https://*source\-name*\.*user\-namespace*\.cloud\.triggermesh\.io  
Include the Basic authorization parameters in the request to the endpoint\.

**Supported authorization types**   
Basic

**Additional authorization parameters required**  
None

**TriggerMesh documentation**  
[Event Source for HTTP](https://docs.triggermesh.io/concepts/sources)

**Commonly used API operations**  
Not applicable

**Additional information**  
None

## Zendesk<a name="eb-api-destination-zendesk"></a>

**API destination invocation endpoint URL**  
https://developer\.zendesk\.com/rest\_api/docs/support/tickets

**Supported authorization types**   
Basic, API Key

**Additional authorization parameters required**  
None

**Zendesk documentation**  
[Security and Authentication](https://developer.zendesk.com/rest_api/docs/support/introduction#security-and-authentication)

**Commonly used API operations**  
POST https://*your\_Zendesk\_subdomain*/api/v2/tickets

**Additional information**  
API requests EventBridge makes count against your Zendesk API limits\. For information about Zendesk limits for your plan, see [Usage limits](https://developer.zendesk.com/rest_api/docs/support/usage_limits)\.   
To better safeguard your account and data, we recommend using an API key rather than basic username and password authentication\.