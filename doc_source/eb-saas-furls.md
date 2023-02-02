# Receiving events using AWS Lambda function URLs<a name="eb-saas-furls"></a>

**Note**  
In order for the Inbound Webhook to be accessible by our partners, we're creating an Open Lambda in your AWS account that is secured at the Lambda application level by verifying the authentication signature sent by the third\-party partner\. Please review this configuration with your security team\. For more information, see [Security and auth model for Lambda function URLs](https://docs.aws.amazon.com/lambda/latest/dg/urls-auth.html#urls-auth-none)\.

Your Amazon EventBridge [event bus](eb-event-bus.md) can use an [AWS Lambda function URL](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html) created by an AWS CloudFormation template to receive [events](eb-events.md) from supported SaaS providers\. With function URLs, the event data is sent to a Lambda function\. The function then converts this data into an event that can be ingested by EventBridge and sent to an event bus for processing\. Once the event is on an event bus, you can use rules to filter the events, apply any configured input transformations, and then route it to the correct target\. 

**Note**  
Creating Lambda function URLs will increase your monthly costs\. For more information, see [AWS Lambda pricing](http://aws.amazon.com/lambda/pricing)\.

To set up a connection to EventBridge, you first select the SaaS provider that you want to set up a connection with\. Then, you provide a *signing secret* that you’ve created with that provider, and select the EventBridge event bus to send events to\. Finally, you use an AWS CloudFormation template and create the needed resources to complete the connection\. 

The following SaaS providers are currently available for use with EventBridge using Lambda function URLs:
+ GitHub
+ Stripe
+ Twilio

**Topics**
+ [Set up a connection to GitHub](#furls-connection-github)
+ [Step 1: Create the AWS CloudFormation stack](#create-gh-cfn-stack)
+ [Step 2: Create a GitHub webhook](#create-gh-webhook)
+ [Set up a connection to a Stripe](#furls-connection-stripe)
+ [Set up a connection to a Twilio](#furls-connection-twilio)
+ [Update webhook secret or auth token](#furls-update-secret)
+ [Update Lambda function](#furls-update-function)
+ [Available event types](#furls-event-types)
+ [Quotas, error codes, and retrying delivery](#furls-quotas-errors)

## Set up a connection to GitHub<a name="furls-connection-github"></a>

## Step 1: Create the AWS CloudFormation stack<a name="create-gh-cfn-stack"></a>

 First, use the Amazon EventBridge console to create a CloudFormation stack:

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. From the navigation pane, choose **Quick starts**\.

1. Under **Inbound webhooks using Lambda fURLs**, choose **Get started**\.

1. Under **GitHub**, choose **Set up**\.

1. Under **Step 1: Select an event bus**, select an event bus from the dropdown list\. This event bus receives data from the Lambda function URL that you provide to GitHub\. You can also create an event bus by selecting **New event bus**\.

1. Under **Step 2: Set up using CloudFormation**, choose **New GitHub webhook**\.

1. Select **I acknowledge that the Inbound Webhook I create will be publicly accessible\.** and choose **Confirm**\.

1. Enter a name for the stack\.

1. Under parameters, verify that the correct event bus is listed, then specify a secure token for the **GitHubWebhookSecret**\. For more information on creating a secure token, see [Setting your secret token](https://docs.github.com/en/developers/webhooks-and-events/webhooks/securing-your-webhooks#setting-your-secret-token) in the GitHub documentation\.

1. Under **Capabilities and transforms**, select each of the following:
   + **I acknowledge that AWS CloudFormation might create IAM resources\.**
   + **I acknowledge that AWS CloudFormation might create IAM resources with custom names\.**
   + **I acknowledge that AWS CloudFormation might require the following capability: `CAPABILITY_AUTO_EXPAND`**

1. Choose **Create stack**\.

## Step 2: Create a GitHub webhook<a name="create-gh-webhook"></a>

Next, create the webhook on GitHub\. You’ll need both the secure token and the Lambda function URL you created in step 2 to complete this step\. For more information, see [Creating webhooks](https://docs.github.com/en/developers/webhooks-and-events/webhooks/creating-webhooks) in the GitHub documentation\.

## Set up a connection to a Stripe<a name="furls-connection-stripe"></a>

### Step 1: Create a Stripe endpoint<a name="create-stripe-secret"></a>

To set up a connection between EventBridge and Stripe, first create a Stripe endpoint and note the endpoint secret\. You'll use this endpoint secret when you set up your stack in step 2\. For more information, see [Interactive webhook endpoint builder](https://stripe.com/docs/webhooks/quickstart) in the Stripe documentation\.

**Note**  
You’ll need a dummy URL to set up the endpoint with Stripe\. For example, `www.example.com`\.

### Step 2: Create the AWS CloudFormation stack<a name="create-stripe-cfn-stack"></a>

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Quick starts**\.

1. Under **Inbound webhooks using Lambda fURLs**, choose **Get started**\.

1. Under **Stripe**, choose **Set up**\.

1. Under **Step 1: Select and event bus**, select an event bus from the dropdown list\. This event bus receives data from the Lambda function URL that you provide to Stripe\. You can also create an event bus by selecting **New event bus**\.

1. Under **Step 2: Set up using CloudFormation**, choose **New Stripe webhook**\.

1. Select **I acknowledge that the Inbound Webhook I create will be publicly accessible\.** and choose **Confirm**\.

1. Enter a name for the stack\.

1. Under parameters, verify that the correct event bus is listed, then enter the **StripeWebhookSecret** that you created in Step 1\.

1. Under **Capabilities and transforms**, select each of the following:
   + **I acknowledge that AWS CloudFormation might create IAM resources\.**
   + **I acknowledge that AWS CloudFormation might create IAM resources with custom names\.**
   + **I acknowledge that AWS CloudFormation might require the following capability: `CAPABILITY_AUTO_EXPAND`**

1. Choose **Create stack**\.

### Step 3: Update the Stripe endpoint<a name="create-stripe-webhook"></a>

Now that you’ve created the Lambda function URL, update the Stripe endpoint to send events to the Lambda function URL\.

## Set up a connection to a Twilio<a name="furls-connection-twilio"></a>

### Step 1: Find your Twilio auth token<a name="create-twilio-secret"></a>

To set up a connection between Twilio and EventBridge, first set up the connection to Twilio with the auth token, or secret, for your Twilio account\. For more information, see [Auth Tokens and How To Change Them](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them) in the Twilio documentation\.

### Step 2: Create the AWS CloudFormation stack<a name="create-twilio-cfn-stack"></a>

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Quick starts**\.

1. Under **Inbound webhooks using Lambda fURLs**, choose **Get started**\.

1. Under **Twilio**, choose **Set up**\.

1. Under **Step 1: Select and event bus**, sselect an event bus from the dropdown list\. This event bus receives data from the Lambda function URL that you provide to Twilio\. You can also create an event bus by selecting **New event bus**\.

1. Under **Step 2: Set up using CloudFormation**, choose **New Twilio webhook**\.

1. Select **I acknowledge that the Inbound Webhook I create will be publicly accessible\.** and choose **Confirm**\.

1. Enter a name for the stack\.

1. Under parameters, verify that the correct event bus is listed, then enter the **TwilioWebhookSecret** that you created in Step 1\.

1. Under **Capabilities and transforms**, select each of the following:
   + **I acknowledge that AWS CloudFormation might create IAM resources\.**
   + **I acknowledge that AWS CloudFormation might create IAM resources with custom names\.**
   + **I acknowledge that AWS CloudFormation might require the following capability: CAPABILITY\_AUTO\_EXPAND**

1. Choose **Create stack**\.

### Step 3: Create a Twilio webhook<a name="create-twilio-webhook"></a>

After you set up the Lambda function URL, you need to give it to Twilio so that event data can be sent\. For more information, see [Configure your public URL with Twilio](https://www.twilio.com/docs/usage/webhooks/getting-started-twilio-webhooks#configure-your-public-url-with-twilio) in the Twilio documentation\.

## Update webhook secret or auth token<a name="furls-update-secret"></a>

### Update GitHub secret<a name="update-gh-secret"></a>

**Note**  
GitHub doesn’t support having two secrets at the same time\. You may experience resource downtime while the GitHub secret and the secret in the AWS CloudFormation stack are out of sync\. GitHub messages sent while the secrets are out of sync will fail becaue of incorrect signatures\. Wait until the GitHub and CloudFormation secrets are in sync, then try again\.

1. Create a new GitHub secret\. For more information, see [Encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) in the GitHub documentation\.

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the navigation pane, choose **Stacks**\.

1. Choose the stack for the webhook that includes the secret you want to update\.

1. Choose **Update**\.

1. Make sure **Use current template** is selected and choose **Next**\.

1. Under **GitHubWebhookSecret**, clear **Use existing value**, enter the new GitHub secret you created in step 1, and choose **Next**\.

1. Choose **Next**\.

1. Choose **Update stack**\.

It may take up to one hour for the secret to propagate\. To reduce this downtime, you can refresh the Lambda execution context\.

### Update Stripe secret<a name="update-stripe-secret"></a>

1. From the Stripe dashboard, in the **Webhooks** section, select **Roll secret** and delay the expiration for at least two\(2\) hours\. For more information, see [Roll endpoint secrets](https://stripe.com/docs/webhooks/best-practices#endpoint-secrets) in the Stripe documentation\.

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the navigation pane, choose **Stacks**\.

1. Choose the stack for the webhook that includes the secret you want to update\.

1. Choose **Update**\.

1. Make sure **Use current template** is selected and choose **Next**\.

1. Under **StripeWebhookSecret**, clear **Use existing value**, enter the new Stripe secret you created in step 1, and choose **Next**\.

1. Choose **Next**\.

1. Choose **Update stack**\.

Stripe will send both the old signature and the new signature during the rotation period\.

### Update Twilio secret<a name="update-twilio-secret"></a>

**Note**  
Twilio doesn’t support having two secrets at the same time\. You may experience resource downtime while the Twilio secret and the secret in the AWS CloudFormation stack are out of sync\. Twilio messages sent while the secrets are out of sync will fail because of incorrect signatures\. Wait until the Twilio and CloudFormation secrets are in sync, then try again\.

1. Create a new Twilio secret\. For more information, see [Auth Tokens and How To Change Them](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them) in the Twilio documentation\.

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the navigation pane, choose **Stacks**\.

1. Choose the stack for the webhook that includes the secret you want to update\.

1. Choose **Update**\.

1. Make sure **Use current template** is selected and choose **Next**\.

1. Under **TwilioWebhookSecret**, clear **Use existing value**, enter the new Twilio secret you created in step 1, and choose **Next**\.

1. Choose **Next**\.

1. Choose **Update stack**\.

It may take up to one hour for the secret to propagate\. To reduce this downtime, you can refresh the Lambda execution context\.

## Update Lambda function<a name="furls-update-function"></a>

The Lambda function that's created by the CloudFormation stack creates the basic webhook\. If you want to customize the Lambda function for a specific use case, such as customized logging, use the CloudFormation console to access the function and then use the Lambda console to update the Lambda function code\.

**Access the Lambda function**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. From the navigation pane, choose **Stacks**\.

1. Choose the stack for the webhook that includes the Lambda function you want to update\.

1. Choose **Resources** tab\.

1. To open the Lambda function in the Lambda console, under **Physical ID**, choose the ID of the Lambda function\.

Now that you've accessed the Lambda function, use the Lambda console to update the function code\.

**Update the Lambda function code**

1. Under **Actions**, choose **Export function**\.

1. Choose **Download deployment package** and save the file to your computer\.

1. Unzip the deployment package \.zip file, update the `app.py` file, and zip the updated deployment package, making sure all the files in the original \.zip file are included\.

1. In the Lambda console, choose the **Code** tab\.

1. Under **Code source**, choose **Upload from**\.

1. Choose **\.zip file**, and then choose **Upload**\. 

   1. In the file chooser, select the file you updated, choose **Open**, and then choose **Save**\.

1. Under **Actions**, choose **Publish new version**\.

## Available event types<a name="furls-event-types"></a>

The following event types are currently supported by CloudFormation event buses:
+ **GitHub** – [All event types](https://docs.github.com/en/developers/webhooks-and-events/webhooks/webhook-events-and-payloads) are supported\.
+ **Stripe** – [All event types](https://stripe.com/docs/api/events/types) are supported\.
+ **Twilio** – [Post\-event webhooks](https://www.twilio.com/docs/chat/webhook-events) are supported\.

## Quotas, error codes, and retrying delivery<a name="furls-quotas-errors"></a>

### Quotas<a name="furls-quotas"></a>

The number of incoming requests to the webhook is capped by the underlying AWS services\. The following table includes the relevant quotas\.


| Service | Quota | 
| --- | --- | 
|  AWS Lambda  |  Default: 10 concurrent executions For more information about quotas, including requesting quota increases, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)\.  | 
|  AWS Secrets Manager  |  Default: 5,000 requests per second For more information about quotas, including requesting quota increases, see [AWS Secrets Manager quotas](https://docs.aws.amazon.com/secretsmanager/latest/userguide/reference_limits.html)\. The number of requests per second is minimized using the [AWS Secrets Manager Python caching client](https://github.com/aws/aws-secretsmanager-caching-python#cache-configuration)\.  | 
|  Amazon EventBridge  |  256KB maximum entry size for PutEvents actions\.  EventBridge enforces Region\-based rate quotas\. For more information, see [EventBridge quotas](eb-quota.md#eb-limits)\.  | 

### Error codes<a name="furls-errors"></a>

Each AWS service returns specific error codes when errors occur\. The following table includes the relevant error codes\.


| Service | Error code | Description | 
| --- | --- | --- | 
|  AWS Lambda  |  429 “TooManyRequestsExption”  |  The concurrent execution quota is exceeded\.  | 
|  AWS Secrets Manager  |  500 “Internal Server Error”  |  The requests per second quota is exceeded\.  | 
|  Amazon EventBridge  |  500 “Internal Server Error”  |  The rate quota is exceeded for the Region\.  | 

### Event redelivery<a name="furls-redelivery"></a>

When errors happen you can retry delivery of the affected events\. Each SaaS provider has different retry procedures\.

#### GitHub<a name="furls-redelivery-github"></a>

Use the GitHub webhooks API to check the deliver status of any webhook call and redeliver the event, if needed\. For more information, see the following GitHub documentation:
+ **Organization** – [Redeliver a delivery for an organization webhook](https://docs.github.com/en/rest/orgs/webhooks#redeliver-a-delivery-for-an-organization-webhook)
+ **Repository** – [Redeliver a delivery for a repository webhook](https://docs.github.com/en/rest/webhooks/repo-deliveries#redeliver-a-delivery-for-a-repository-webhook)
+ **App** – [Redeliver a delivery for an app webhook](https://docs.github.com/en/rest/apps/webhooks#redeliver-a-delivery-for-an-app-webhook)

#### Stripe<a name="furls-redelivery-stripe"></a>

Stripe attempts to deliver your webhooks for up to three days with an exponential back off\. For more information, see the following Stripe documentation:
+ [Delivery attempts and retries](https://stripe.com/docs/webhooks/best-practices#events-and-retries)
+ [Handle errors](https://stripe.com/docs/error-handling)

#### Twilio<a name="furls-redelivery-twilio"></a>

Twilio users can customize event retry options using connection overrides\. For more information, see [Webhooks \(HTTP callbacks\): Connection Overrides ](https://www.twilio.com/docs/usage/webhooks/webhooks-connection-overrides) in the Twilio documentation\.