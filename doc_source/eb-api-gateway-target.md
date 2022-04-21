# Amazon EventBridge targets for Amazon API Gateway<a name="eb-api-gateway-target"></a>

You can use Amazon API Gateway to create, publish, maintain, and monitor REST APIs\. Amazon EventBridge supports sending events to an API Gateway REST endpoint\. When you specify an API Gateway endpoint as a [target](eb-targets.md), each [event](eb-events.md) sent to the target maps to a request sent to the endpoint\.

**Important**  
EventBridge supports using API Gateway *Edge\-optimized* and *Regional* endpoints as targets\. *Private* endpoints are not currently supportted. To learn more about endpoints, see [https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-endpoint-types.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-endpoint-types.html)\.

You can use an API Gateway target for the following use cases:
+ To invoke a customer\-specified REST API hosted in API Gateway based on AWS or third\-party events\.
+ To invoke an endpoint periodically on a schedule\.

The EventBridge JSON event information is sent as the body of the HTTP request to your endpoint\. You can specify the other request attributes in the target’s `HttpParameters` field as follows:
+ `PathParameterValues` lists the values that correspond sequentially to any path variables in your endpoint ARN, for example `"arn:aws:execute-api:us-east-1:112233445566:myapi/*/POST/pets/*"`\.
+ `QueryStringParameters` represents the query string parameters that EventBridge appends to the invoked endpoint\.
+ `HeaderParameters` defines HTTP headers to add to the request\.

**Note**  
For security considerations, the following HTTP header keys aren't permitted:  
Anything prefixed with `X-Amz` or `X-Amzn`
`Authorization`
`Connection`
`Content-Encoding`
`Content-Length`
`Host`
`Max-Forwards`
`TE`
`Transfer-Encoding`
`Trailer`
`Upgrade`
`Via`
`WWW-Authenticate`
`X-Forwarded-For`

## Dynamic Parameters<a name="eb-targets-apigateway-dynamic"></a>

When invoking an API Gateway target, you can dynamically add data to events that are sent to the target\. For more information, see [Target parameters](eb-targets.md#targets-specific-parms)\.

## Invocation Retries<a name="eb-targets-apigateway-retries"></a>

As with all targets, EventBridge retries some failed invocations\. For API Gateway, EventBridge retries responses sent with a 5xx or 429 HTTP status code for up to 24 hours with [exponential back off and jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\. After that, EventBridge publishes a `FailedInvocations` metric in Amazon CloudWatch\. EventBridge doesn't retry other 4xx HTTP errors\.

## Timeout<a name="eb-targets-apigateway-timeout"></a>

EventBridge API Gateway requests must have a maximum client execution timeout of 5 seconds\. If API Gateway takes longer than 5 seconds to respond, EventBridge times out the request and then retries
