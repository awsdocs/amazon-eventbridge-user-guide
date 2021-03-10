# API Gateway Target<a name="targets-apigateway"></a>

API Gateway enables customers to create, publish, maintain, and monitor REST APIs\. Amazon EventBridge supports sending events to an API Gateway REST endpoint\. When you specify an API Gateway endpoint as a target, each event sent to the target maps to a request sent to the endpoint\.

**Important**  
EventBridge does not support using API Gateway private endpoints as targets\. To learn more about private endpoints, see [Introducing Amazon API Gateway Private Endpoints](https://aws.amazon.com/blogs/compute/introducing-amazon-api-gateway-private-endpoints/)\.

You can use the API Gateway target for the following use cases:
+ Invoke a customer\-specified REST API hosted in API Gateway in an event\-driven manner based on AWS or third\-party events\.
+ Invoke an endpoint periodically based on a schedule\.

The EventBridge JSON event payload is sent as the body of the HTTP request to your endpoint\. You can specify the other request attributes in the rule Target’s `HttpParameters` field as follows:
+ The `PathParameterValues` list values that correspond sequentially to any path variables in your endpoint ARN \(for example `"arn:aws:execute-api:us-east-1:112233445566:myapi/*/POST/pets/*"`\)\.
+ The `QueryStringParameters` map represents keys/values of query string parameters that are appended to the invoked endpoint\.
+ The `HeaderParameters` map enables you to specify HTTP headers to add to the request\.

**Note**  
For security considerations, the following HTTP header keys are not currently permitted:  
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

## Dynamic Parameters<a name="targets-apigateway-dynamic"></a>

Any `HttpParameters` field value \(except `HeaderParameters` map keys\) can contain JSON paths instead of static values \(for example `$.detail.state`\)\. Such paths are replaced dynamically at runtime with data from the event payload itself at the specified path\. The supported syntax for dynamic parameter JSON paths is the same as for Target InputTransformers\.

**Note**  
The dynamic parameter syntax is only supported for API Gateway target invocations\.

## Invocation Retries<a name="targets-apigateway-retries"></a>

As with all targets, EventBridge will retry some failed invocations\. For API Gateway, EventBridge will retry responses sent with a 5xx or 429 HTTP status code for up to 24 hours with exponential backoff and jitter\. After that, a `FailedInvocations` metric is published in Amazon CloudWatch\. Other 4xx errors are not retried\.

## Timeout<a name="targets-apigateway-timeout"></a>

EventBridge API Gateway requests must have a maximum client execution timeout of 5 seconds\. If API Gateway takes longer than 5 seconds to respond, EventBridge will timeout the request and then retry\.