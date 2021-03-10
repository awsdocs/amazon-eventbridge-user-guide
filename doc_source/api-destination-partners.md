# API destination partners<a name="api-destination-partners"></a>

Use the information provided by the partners in this section to configure an API destination and connection for their service or application\.

**Topics**
+ [Datadog](#api-destination-datadog)
+ [Freshworks](#api-destination-freshworks)
+ [MongoDB](#api-destination-mongodb)
+ [Splunk](#api-destination-splunk)
+ [TriggerMesh](#api-destination-triggermesh)
+ [Zendesk](#api-destination-zendesk)

## Datadog<a name="api-destination-datadog"></a>

**API destination invocation endpoint URL:**  
https://api\.datadoghq\.com/api/v1/events  
This endpoint differs depending on where you created your Datadog organization\. To view a full list of endpoints, see [Post an Event](https://docs.datadoghq.com/api/latest/events/#post-an-event) in the Datadog documentation\.

**Supported authorization types:**   
API Key

**Additional authorization parameters required:**  
None

**Datadog documentation:**  
[Authentication](https://docs.datadoghq.com/api/latest/authentication/)

**Commonly used API operations:**  
POST https://api\.datadoghq\.com/api/v1/events

**Additional information:**  
There are two required fields: an event title and the event text\. Event titles are limited to 100 characters, and the text is limited to 4000 characters\.

## Freshworks<a name="api-destination-freshworks"></a>

**API destination invocation endpoint URL:**  
View endpoints at [https://developers\.freshworks\.com/documentation/](https://developers.freshworks.com/documentation/)

**Supported authorization types:**   
Basic, API Key

**Additional authorization parameters required:**  
Not applicable

**Freshworks documentation:**  
[Authentication](https://developers.freshdesk.com/api/#authentication)

**Commonly used API operations:**  
https://developers\.freshdesk\.com/api/\#create\_ticket  
https://developers\.freshdesk\.com/api/\#update\_ticket  
https://developer\.freshsales\.io/api/\#create\_lead  
https://developer\.freshsales\.io/api/\#update\_lead

**Additional information:**  
API Key authentication is recommended over Basic authentication\.  
APIs are subject to rate limits\. For details, see [Rate Limit](https://developers.freshdesk.com/api/#ratelimit)\.

## MongoDB<a name="api-destination-mongodb"></a>

**API destination invocation endpoint URL:**  
https://webhooks\.mongodb\-realm\.com/api/client/v2\.0/app/*Realm App ID*/service/*HTTP Service Name*/incoming\_webhook/*Webhook Name*

**Supported authorization types:**   
API Key

**Additional authorization parameters required:**  
None

**MongoDB documentation:**  
[API Key](https://docs.mongodb.com/realm/services/configure/service-webhooks/)  
[Authentication Providers](https://docs.mongodb.com/realm/authentication/providers/)

**Commonly used API operations:**  
POST https://webhooks\.mongodb\-realm\.com/api/client/v2\.0/app/*Realm App ID*/service/*HTTP Service Name*/incoming\_webhook/*Webhook Name*

**Additional information:**  
[EventbridgeAtlas](https://github.com/mongodb-developer/EventbridgeAtlas) demonstrates how Realm Webhooks can be invoked to perform CRUD operations on collections residing in MongoDB Atlas from EventBridge API Endpoints option\.

## Splunk<a name="api-destination-splunk"></a>

**API destination invocation endpoint URL:**  
https://*SPLUNK\_HEC\_ENDPOINT*:*optional\_port*/services/collector/raw

**Supported authorization types:**   
Basic, API Key

**Additional authorization parameters required:**  
None

**Splunk documentation:**  
For both authorization types, an HEC token ID is required\. To learn more, see [Set up and use HTTP Event Collector in Splunk Web](https://docs.splunk.com/Documentation/Splunk/8.1.2/Data/UsetheHTTPEventCollector)\.

**Commonly used API operations:**  
POST https://*SPLUNK\_HEC\_ENDPOINT*:*optional\_port*/services/collector/raw

**Additional information:**  
API Key – When configuring the endpoint for EventBridge, the API key name is “Authorization” and value is the “Splunk HEC token ID”  
Basic \(Username/Password\) – When configuring the endpoint for EventBridge, the username is “Splunk” and the password is “HEC tokenID”

## TriggerMesh<a name="api-destination-triggermesh"></a>

**API destination invocation endpoint URL:**  
Use the information in the [Event Source for HTTP](https://docs.triggermesh.io/sources/http/) topic to formulate the endpoint URL\. An endpoint URL includes the event source name and user namespace in the following format:  
https://*source\-name*\.*user\-namespace*\.cloud\.triggermesh\.io  
Include the Basic authorization parameters in the request to the endpoint\.

**Supported authorization types:**   
Basic

**Additional authorization parameters required:**  
None

**TriggerMesh documentation:**  
[Event Source for HTTP](https://docs.triggermesh.io/sources/http/)

**Commonly used API operations:**  
Not applicable

**Additional information:**  
None

## Zendesk<a name="api-destination-zendesk"></a>

**API destination invocation endpoint URL:**  
https://developer\.zendesk\.com/rest\_api/docs/support/tickets

**Supported authorization types:**   
Basic, API Key

**Additional authorization parameters required:**  
None

**Zendesk documentation:**  
[Security and Authentication](https://developer.zendesk.com/rest_api/docs/support/introduction#security-and-authentication)

**Commonly used API operations:**  
POST https://*your\_Zendesk\_subdomain*/api/v2/tickets

**Additional information:**  
API requests made by EventBridge will count against your Zendesk API limits\. You can read more about Zendesk limits for your plan type here \(https://developer\.zendesk\.com/rest\_api/docs/support/usage\_limits\)\. To better safeguard your account and data, we recommend using an API key rather than basic \(username/password\) authentication\.