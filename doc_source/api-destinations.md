# API destinations<a name="api-destinations"></a>

EventBridge API destinations are HTTP endpoints that you can invoke as the target of a rule similar to invoking an AWS service or resource as a target\. This lets you route events between AWS services, integrated SaaS applications, and your own applications outside of AWS using REST API calls\. When you specify an API destination as the target of a rule, EventBridge invokes the HTTP endpoint for any event that matches the event pattern specified in the rule and delivers the event payload with the request\. EventBridge supports using any HTTP method \(except CONNECT and TRACE\) for the request, such as PUT or POST\. You can also use input transformers to customize the event payload to the parameters of a specific HTTP endpoint parameters\. To learn more, see [Transforming Target Input](transform-input.md)\.

**Important**  
EventBridge requests to the API destination endpoint must have a maximum client execution timeout of 5 seconds\. If the target invocation endpoint takes longer than 5 seconds to respond, EventBridge times out the request\. Timed out requests are retried up to the maximum configured on your optional retry policy \(default 24 hours, 185 times\)\. After the maximum number of retries, events are sent to your [dead\-letter queue](rule-dlq.md) if you configured one\. If not, the event is dropped\.

**Topics**
+ [Connections for API destinations](#api-destination-connection)
+ [Create an API destination](#api-destination-create)
+ [Service\-linked role for API destinations](#api-destination-slr)
+ [API destination error codes](#api-destination-error-codes)
+ [How invocation rate affects event delivery](#api-destination-event-delivery)
+ [API destination partners](api-destination-partners.md)

## Connections for API destinations<a name="api-destination-connection"></a>

When you create an API destination, you specify a connection to use for it\. Each connection includes the details about the authorization type and parameters to use to authorize with the API destination endpoint\. You can choose an existing connection from your account, or create a connection when you create an API destination\. EventBridge supports Basic, OAuth, and API Key authorization\. For Basic and API Key authorization, EventBridge populates the requisite authorization headers for your invocation\. For OAuth authorization, EventBridge also exchanges your client ID and secret for an access token, and manages it securely\. When you create a connection you can also include additional header, body, and query parameters required for authorization with an endpoint\. You can use the same connection for more than one API destination if the authorization for the endpoints is the same\. When you create a connection and add authorization parameters for the selected type, the data is used to create a secret in AWS Secrets Manager\. The cost of storing the Secrets Manager secret is included with the charge for using an API destination\.

**Note**  
To successfully create or update a connection, you must use an account that has permissions to use Secrets Manager\. The required permissions are included in the [AmazonEventBridgeFullAccess policy](iam-identity-based-access-control-eventbridge.md#eb-full-access-policy)\. The same permissions are granted to the [service\-linked role](#api-destination-slr) created in your account for the connection\.

**To create a connection**

1. Log in to AWS using an account that has permissions to manage EventBridge and open the [EventBridge console](https://console.aws.amazon.com/events)\.

1. In the left navigation pane, choose **API destinations**\.

1. Scroll down to the **API destinations** table, then choose the **Connections** tab\.

1. Choose **Create connection**\.

1. On the **Create connection** page, enter a **Connection name** for the connection\.

1. Enter a **Description** for the connection\.

1. For **Authorization type**, select the type of authorization to use to authorize connections to the HTTP endpoint specified for the API destination that uses this connection\. Do one of the following:
   + Choose **Basic \(Username/Password\)**, then enter the **Username** and **Password** to use to authorize with the HTTP endpoint\.
   + Choose **OAuth Client Credentials**, then enter the **Authorization endpoint**, **HTTP method**, **Client ID**, and **Client secret** to use to authorize with the endpoint\.

     Under **OAuth Http Parameters**, add any additional parameters to include for authorization with the authorization endpoint\. Select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.

     Under **Invocation Http Parameters**, you can include any additional parameters to include in the authorization request\. To add a parameter, select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.
   + Choose **API key**, and then enter the **API key name** and associated **Value** to use for API Key authorization\.

     Under **Invocation Http Parameters**, you can include any additional parameters to include in the authorization request\. To add a parameter, select a **Parameter** from the drop\-down list, then enter a **Key** and **Value**\. To include an additional parameter, choose **Add parameter**\.

1. Choose **Create**\.

**To edit a connection**

1. Open the **API destinations** page then choose **Connections**\.

1. Choose the connection to edit from the **Connections** table\.

1. On the **Connection details** page, choose **Edit**\.

1. Update the values for the connection and then choose **Update**\.

### De\-authorizing connections<a name="api-destination-deauthorize"></a>

When you de\-authorize a connection, it removes all authorization parameters\. This lets you remove the secret from the connection so you can reuse it without having to create a new connection\. You must update any API destinations that use the de\-authorized connection to use a different connection to successfully send requests to the API destination endpoint\.

**To de\-authorize a connection**

1. Choose the connection from the Connections table\.

1. On the **Connection details** page, choose **De\-authorize**\.

1. On the **Deauthorize connection?** dialog, enter the name of the connection and then choose **De\-authorize**\.

The status of the connection changes to **De\-authorizing** until the process is complete and the status changes to **De\-authorized**\. You can then edit the connection to add new authorization parameters\.

## Create an API destination<a name="api-destination-create"></a>

Each API destination requires a connection\. The connection specifies the authorization type and credentials to use to authorize with the API destination endpoint\. You can choose an existing connection, or create a connection at the same time that you create the API destination\.

**To create an API destination**

1. Log in to AWS using an account that has permissions to manage EventBridge and open the [EventBridge console](https://console.aws.amazon.com/events)\.

1. In the left navigation pane, choose **API destinations**\.

1. Scroll down to the **API destinations** table, then choose **Create API destination**\.

1. On the **Create API destination** page, enter a **Name** for the API destination\. You can use up to 64 upper or lower\-case letters, numbers, dot \(\.\), dash \(\-\), or underscore \(\_\) characters\.

   The name must be unique to your account in the current Region\.

1. Enter a **Description** for the API destination\.

1. Enter a **API destination endpoint** for the API destination\. The **API destination endpoint** is an HTTP invocation endpoint target for events\. The authorization information you include in the connection used for this API destination is used to authorize against this endpoint\. The URL must use HTTPS\.

1. Enter the **HTTP method** to use to connect to the **API destination endpoint**\.

1. For **Invocation rate limit per second** field, optionally enter the maximum number of invocations per second to send to the API destination endpoint\.

   The rate limit you set may impact event delivery\. To learn more, see [How invocation rate affects event delivery](#api-destination-event-delivery)\.

1. For **Connection**, do one of the following:
   + Choose **Use an existing connection**, then select the connection to use for this API destination\.
   + Choose **Create a new connection**, then enter the details for the connection to create\. To learn more, see [Connections](#api-destination-connection)\.

1. Choose **Create**\.

After you create an API destination you can select it as the target of a rule\.

## Service\-linked role for API destinations<a name="api-destination-slr"></a>

When you create a connection for an API destination, a service\-linked role named AWSServiceRoleForAmazonEventBridgeApiDestinations is added to your account\. The service\-linked role is used to create and store a secret in Secrets Manager\. To grant the necessary permissions to the service\-linked role, the AmazonEventBridgeApiDestinationsServiceRolePolicy policy is attached to the role\. The policy limits the permissions granted to only those necessary for the role to interact with the secret for the connection\. No other permissions are included, and the role can interact only with the connections in your account to manage the secret\.

The AmazonEventBridgeApiDestinationsServiceRolePolicy policy:

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

To learn more about service\-linked roles, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the IAM documentation\.

## API destination error codes<a name="api-destination-error-codes"></a>

When an error occurs with event delivery to an API destination, events are retried as follows:

Events associated with error codes 429 and 5xx are retried\.

Events associated with error codes 1xx, 2xx, 3xx, and 4xx \(excluding 429\) are not retried\.

EventBridge API destinations respects the standard HTTP response header `Retry-After`, which indicates how long the user agent should wait before making a follow\-up request\. EventBridge chooses the more conservative value between the defined retry policy and the Retry\-After header\. If a negative Retry\-After value exists EventBridge stops retrying delivery for that event\. 

## How invocation rate affects event delivery<a name="api-destination-event-delivery"></a>

If you set the invocation rate per second to a value much lower than the number of invocations generated, events may not be delivered within the 24 hour retry time for events\. For example, if you set the invocation rate to 10 invocations per second, but thousands of events per second are generated, you will quickly have a backlog of events to deliver that exceeds 24 hours\. To ensure that no events are lost, set up a dead\-letter queue to send events with failed invocations to so you can process the events at a later time\. To learn more, see [Event retry policy and using dead\-letter queues](rule-dlq.md)\.