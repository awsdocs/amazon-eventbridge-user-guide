# API destinations<a name="eb-api-destinations"></a>

Amazon EventBridge *API destinations* are HTTP endpoints that you can invoke as the [target](eb-targets.md) of a [rule](eb-rules.md), similar to how you invoke an AWS service or resource as a target\. Using API destinations, you can route [events](eb-events.md) between AWS services, integrated software as a service \(SaaS\) applications, and your applications outside of AWS by using REST API calls\. When you specify an API destination as the target of a rule, EventBridge invokes the HTTP endpoint for any event that matches the [event pattern](eb-event-patterns.md) specified in the rule and then delivers the event information with the request\. With EventBridge, you can use any HTTP method except CONNECT and TRACE for the request\. The most common HTTP methods to use are PUT and POST\. You can also use input transformers to customize the event to the parameters of a specific HTTP endpoint parameters\. For more information, see [Transforming Amazon EventBridge target input](eb-transform-target-input.md)\.

**Important**  
EventBridge requests to an API destination endpoint must have a maximum client execution timeout of 5 seconds\. If the target endpoint takes longer than 5 seconds to respond, EventBridge times out the request\. EventBridge retries timed out requests up to the maximums that are configured on your retry policy\. By default the maximums are 24 hours and 185 times\. After the maximum number of retries, events are sent to your [dead\-letter queue](eb-rule-dlq.md) if you have one\.Otherwise, the event is dropped\.



**Topics**
+ [Connections for API destinations](#eb-api-destination-connection)
+ [Create an API destination](#eb-api-destination-create)
+ [Service\-linked role for API destinations](#eb-api-destination-slr)
+ [Headers included in requests to API destinations](#eb-api-destination-headers)
+ [API destination error codes](#eb-api-destination-error-codes)
+ [How invocation rate affects event delivery](#eb-api-destination-event-delivery)
+ [API destination partners](eb-api-destination-partners.md)

## Connections for API destinations<a name="eb-api-destination-connection"></a>

When you create an API destination, you specify a connection to use for it\. A *connection* specifies the authorization type and parameters to use to authorize with the API destination endpoint\. You can choose an existing connection from your account or create a connection when you create an API destination\. EventBridge supports Basic, OAuth, and API Key authorization\.

For Basic and API Key authorization, EventBridge populates the required authorization headers for you\. For OAuth authorization, EventBridge also exchanges your client ID and secret for an access token and then manages it securely\. When you create a connection, you can also include the header, body, and query parameters that are required for authorization with an endpoint\. You can use the same connection for more than one API destination if the authorization for the endpoint is the same\.

When you create a connection and add authorization parameters, EventBridge creates a secret in AWS Secrets Manager\. The cost of storing the Secrets Manager secret is included with the charge for using an API destination\. To learn more about best practices for using secrets with API destinations, see [AWS::Events::ApiDestination](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-events-apidestination.html) in the *CloudFormation User Guide*\.

**Note**  
To successfully create or update a connection, you must use an account that has permission to use Secrets Manager\. The required permission is included in the [AmazonEventBridgeFullAccess policy](eb-use-identity-based.md#eb-full-access-policy)\. The same permission is granted to the [service\-linked role](#eb-api-destination-slr) that's created in your account for the connection\.

**To create a connection**

1. Log in to AWS using an account that has permissions to manage EventBridge and open the [EventBridge console](https://console.aws.amazon.com/events)\.

1. In the left navigation pane, choose **API destinations**\.

1. Scroll down to the **API destinations** table, and then choose the **Connections** tab\.

1. Choose **Create connection**\.

1. On the **Create connection** page, enter a **Connection name** for the connection\.

1. Enter a **Description** for the connection\.

1. For **Authorization type**, select the type of authorization to use to authorize connections to the HTTP endpoint specified for the API destination that uses this connection\. Do one of the following:
   + Choose **Basic \(Username/Password\)**, and then enter the **Username** and **Password** to use to authorize with the HTTP endpoint\.
   + Choose **OAuth Client Credentials**, and then enter the **Authorization endpoint**, **HTTP method**, **Client ID**, and **Client secret** to use to authorize with the endpoint\.

     Under **OAuth Http Parameters**, add any additional parameters to include for authorization with the authorization endpoint\. Select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.

     Under **Invocation Http Parameters**, add any additional parameters to include in the authorization request\. To add a parameter, select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.
   + Choose **API key**, and then enter the **API key name** and associated **Value** to use for API Key authorization\.

     Under **Invocation Http Parameters**, add any additional parameters to include in the authorization request\. To add a parameter, select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.

1. Choose **Create**\.

**To edit a connection**

1. Open the **API destinations** page, and then choose **Connections**\.

1. In the **Connections** table, choose the connection to edit\.

1. On the **Connection details** page, choose **Edit**\.

1. Update the values for the connection, and then choose **Update**\.

### De\-authorizing connections<a name="eb-api-destination-deauthorize"></a>

When you de\-authorize a connection, it removes all authorization parameters\. Removing authorization parameters removes the secret from the connection, so you can reuse it without having to create a new connection\.

**Note**  
You must update any API destinations that use the de\-authorized connection to use a different connection to successfully send requests to the API destination endpoint\.

**To de\-authorize a connection**

1. In the **Connections** table, choose the connection\.

1. On the **Connection details** page, choose **De\-authorize**\.

1. In the **Deauthorize connection?** dialog box, enter the name of the connection, and then choose **De\-authorize**\.

The status of the connection changes to **De\-authorizing** until the process is complete\. Then the status changes to **De\-authorized**\. Now you can edit the connection to add new authorization parameters\.

## Create an API destination<a name="eb-api-destination-create"></a>

Each API destination requires a connection\. A *connection* specifies the authorization type and credentials to use to authorize with the API destination endpoint\. You can choose an existing connection, or create a connection at the same time that you create the API destination\.

**To create an API destination**

1. Log in to AWS using an account that has permissions to manage EventBridge and open the [EventBridge console](https://console.aws.amazon.com/events)\.

1. In the left navigation pane, choose **API destinations**\.

1. Scroll down to the **API destinations** table, and then choose **Create API destination**\.

1. On the **Create API destination** page, enter a **Name** for the API destination\. You can use up to 64 uppercase or lowercase letters, numbers, dot \(\.\), dash \(\-\), or underscore \(\_\) characters\.

   The name must be unique to your account in the current Region\.

1. Enter a **Description** for the API destination\.

1. Enter an **API destination endpoint** for the API destination\. The **API destination endpoint** is an HTTP invocation endpoint target for events\. The authorization information you include in the connection used for this API destination is used to authorize against this endpoint\. The URL must use HTTPS\.

1. Enter the **HTTP method** to use to connect to the **API destination endpoint**\.

1. \(Optional\) For **Invocation rate limit per second** field, enter the maximum number of invocations per second to send to the API destination endpoint\.

   The rate limit you set may affect how EventBridge delivers events\. For more information, see [How invocation rate affects event delivery](#eb-api-destination-event-delivery)\.

1. For **Connection**, do one of the following:
   + Choose **Use an existing connection**, and then select the connection to use for this API destination\.
   + Choose **Create a new connection**, and then enter the details for the connection to create\. For more information, see [Connections](#eb-api-destination-connection)\.

1. Choose **Create**\.

After you create an API destination, you can select it as the target of a [rule](eb-rules.md)\. To use an API destination as a target, you must provide an IAM role with the correct permissions\. For more information, see [Permissions required for EventBridge to access targets using IAM roles](eb-use-identity-based.md#eb-target-permissions)

## Service\-linked role for API destinations<a name="eb-api-destination-slr"></a>

When you create a connection for an API destination, a service\-linked role named **AWSServiceRoleForAmazonEventBridgeApiDestinations** is added to your account\. EventBridge uses the service\-linked role to create and store a secret in Secrets Manager\. To grant the necessary permissions to the service\-linked role, EventBridge attaches the **AmazonEventBridgeApiDestinationsServiceRolePolicy** policy to the role\. The policy limits the permissions granted to only those necessary for the role to interact with the secret for the connection\. No other permissions are included, and the role can interact only with the connections in your account to manage the secret\.

The following policy is the AmazonEventBridgeApiDestinationsServiceRolePolicy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:CreateSecret",
                "secretsmanager:UpdateSecret",
                "secretsmanager:DescribeSecret",
                "secretsmanager:DeleteSecret",
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:events!connection/*"
        }
    ]
}
```

For more information about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the IAM documentation\.

## Headers included in requests to API destinations<a name="eb-api-destination-headers"></a>

In addition to the authorization headers defined for the connection used for an API destination, EventBridge includes the following headers in each request\.


| Header key | Header value | 
| --- | --- | 
|  User\-Agent  |  Amazon/EventBridge/ApiDestinations  | 
|  Content\-Type  |  application/json; charset=utf\-8  | 
|  Range  |  bytes=0\-1048575  | 
|  Accept\-Encoding  |  gzip,deflate  | 
|  Connection  |  close  | 
|  Content\-Length  |  An entity header that indicates the size of the entity\-body, in bytes, sent to the recipient\.  | 
|  Host  |  A request header that specifies the host and port number of the server where the request is being sent\.  | 

## API destination error codes<a name="eb-api-destination-error-codes"></a>

When EventBridge tries to deliver an event to an API destination and an error occurs, EventBridge does the following:
+ Events associated with error codes 429 and 5xx are retried\.
+ Events associated with error codes 1xx, 2xx, 3xx, and 4xx \(excluding 429\) aren't retried\.

EventBridge API destinations read the standard HTTP response header `Retry-After` to find out how long to wait before making a follow\-up request\. EventBridge chooses the more conservative value between the defined retry policy and the `Retry-After` header\. If `Retry-After` value is negative, EventBridge stops retrying delivery for that event\.

## How invocation rate affects event delivery<a name="eb-api-destination-event-delivery"></a>

If you set the invocation rate per second to a value much lower than the number of invocations generated, events may not be delivered within the 24 hour retry time for events\. For example, if you set the invocation rate to 10 invocations per second, but thousands of events per second are generated, you will quickly have a backlog of events to deliver that exceeds 24 hours\. To ensure that no events are lost, set up a dead\-letter queue to send events with failed invocations to so you can process the events at a later time\. For more information, see [Event retry policy and using dead\-letter queues](eb-rule-dlq.md)\.