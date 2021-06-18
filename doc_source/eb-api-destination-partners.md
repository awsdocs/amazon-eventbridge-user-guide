# API destination partners<a name="eb-api-destination-partners"></a>

Use the information provided by the following AWS Partners to configure an API destination and connection for their service or application\.

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
For a full list of endpoints, see [Salesforce API Reference](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/resources_list.htm)

**Supported authorization types**   
OAuth client credentials

**Additional authorization parameters required**  
grant\_type  
username  
password

**Salesforce documentation**  
[REST API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_what_is_rest_api.htm)

**Commonly used API operations**  
[Working with Object Metadata](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/using_resources_working_with_object_metadata.htm)  
[Working with Records](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/using_resources_working_with_records.htm)

**Additional information**  
[Salesforce Connected App](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_oauth_and_connected_apps.htm) provides the client ID and client secret\.  
The grant\_type value is “password”\.  
Username and password values are from Salesforce user credentials\.

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
API Key – When configuring the endpoint for EventBridge, the API key name is “Authorization” and value is the “Splunk HEC token ID”\.  
Basic \(Username/Password\) – When configuring the endpoint for EventBridge, the username is “Splunk” and the password is “HEC tokenID”\.

## TriggerMesh<a name="eb-api-destination-triggermesh"></a>

**API destination invocation endpoint URL**  
Use the information in the [Event Source for HTTP](https://docs.triggermesh.io/sources/http/) topic to formulate the endpoint URL\. An endpoint URL includes the event source name and user namespace in the following format:  
https://*source\-name*\.*user\-namespace*\.cloud\.triggermesh\.io  
Include the Basic authorization parameters in the request to the endpoint\.

**Supported authorization types**   
Basic

**Additional authorization parameters required**  
None

**TriggerMesh documentation**  
[Event Source for HTTP](https://docs.triggermesh.io/sources/http/)

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