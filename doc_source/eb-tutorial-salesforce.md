# Tutorial: Create a connection to Salesforce as an API destination<a name="eb-tutorial-salesforce"></a>

You can use EventBridge to route [events](eb-events.md) to third\-party services, such as [https://www.salesforce.com/](https://www.salesforce.com/)\.

In this tutorial, you'll use the EventBridge console to create a connection to Salesforce, an [API destination](eb-api-destinations.md) that points to Salesforce, and a [rule](eb-rules.md) to route events to Salesforce\. 

**Topics**
+ [Prerequisites](#eb-sf-prereqs)
+ [Step 1: Create connection](#eb-sf-create-connection)
+ [Step 2: Create API destination](#eb-dd-api-destination)
+ [Step 3: Create rule](#eb-dd-create-rule)
+ [Step 4: Test the rule](#eb-dd-test-rule)
+ [Step 5: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-sf-prereqs"></a>

To complete this tutorial, you'll need the following resources:
+ A [Salesforce account](https://login.salesforce.com/)\.
+ A [Salesforce connected app](https://help.salesforce.com/s/articleView?id=sf.connected_app_create_basics.htm)\.
+ A [Salesforce security token](https://help.salesforce.com/s/articleView?id=sf.user_security_token.htm)\.
+ A [Salesforce custom platform event](https://developer.salesforce.com/docs/atlas.en-us.234.0.platform_events.meta/platform_events/platform_events_define.htm)\.
+ An EventBridge\-enabled [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/Welcome.html) bucket\.

## Step 1: Create connection<a name="eb-sf-create-connection"></a>

To send events to Salesforce, you'll first have to establish a connection to the Salesforce API\.

**To create the connection**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose the **Connections** tab, and then choose **Create connection**\.

1. Enter a name and description for the connection\. For example, enter **Salesforce** as a name, and **Salesforce API Connection** as a description\.

1. For **Destination type**, choose **Partners** and for **Partner Destinations**, select Salesforce from the drop\-down list\.

1. For **Authorization endpoint**, enter one of these:
   + If you're using a production org, enter **https://*MyDomainName*\.my\.salesforce\.com\./services/oauth2/token**
   + If you're using a sandbox without enhanced domains, enter **https://*MyDomainName*\-\-*SandboxName*\.my\. salesforce\.com/services /oauth2/token**
   + If you're using a sandbox with enhanced domains, enter **https://*MyDomainName*\-\-* SandboxName*\.sandbox\.my\.salesforce\.com/services/oauth2/token**

1. For **HTTP method**, choose **POST** from the drop\-down list\.

1. For **Client ID**, enter the client ID from your Salesforce connected app\.

1. For **Client secret**, enter the client secret from your Salesforce connected app\.

1. For **OAuth Http Parameters**, enter the following key/value pairs:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/eventbridge/latest/userguide/eb-tutorial-salesforce.html)

1. Choose **Create**\.

## Step 2: Create API destination<a name="eb-dd-api-destination"></a>

Now that you've created the connection, next you'll create the API destination to use as the [target](eb-targets.md) of the rule\.

**To create the API Destination**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose **Create API destination**\.

1. Enter a name and description for the API destination\. For example, enter **SalesforceAD** for the name, and **Salesforce API Destination** for the description\.\.

1. For **API destination endpoint**, enter **https://*MyDomainName*\.my\.salesforce\.com/services/data/v54\.0/sobjects/*MyEvent\_\_e*** where **Myevent\_\_e** is the platform event where you want to send information\.

1. For **HTTP method**, choose **POST** from the drop\-down list\.

1. For **Invocation rate limit**, enter **300**\.

1. For **Connection**, choose **Use an existing connection** and choose the `Salesforce` connection you created in step 1\.

1. Choose **Create**\.

## Step 3: Create rule<a name="eb-dd-create-rule"></a>

Next, you'll create a rule to send events to Salesforce when an Amazon S3 object is created\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, enter **SalesforceRule** for the name, and **Rule to send events to Salesforce for S3 object creation** for the description\.

1. For **Event bus**, choose **default**\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **Other**\.

1. For **Event pattern**, enter the following:

   ```
   {
     "source": ["aws.s3"]
   }
   ```

1. Choose **Next**\.

1. For **Target types**, choose **EventBridge API destination**\.

1. For **API destination**, choose **Use an existing API destination**, and then choose the `SalesforceAD` destination you created in step 2\.

1. For **Execution role**, choose **Create a new for role for this specific resource**\.

1. For **Additional settings**, do the following:

   1. For **Configure target input**, choose **Input transformer** from the drop\-down list\.

   1. Choose **Configure input transformer**

   1. for **Sample events**, enter the following:

      ```
      {
        "detail":[]
      }
      ```

   1. For **Target input transformer** do the following:

      1. For **Input Path**, enter the following:

         ```
         {"detail":"$.detail"}
         ```

      1. For **Input Template**, enter the following:

         ```
         {"message": <detail>}
         ```

   1. Choose **Confirm\.**\.

1. Choose **Next**\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 4: Test the rule<a name="eb-dd-test-rule"></a>

To test your rule, create an [Amazon S3 object](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html) by uploading a file to an EventBridge\-enabled bucket\. The information about the created object will be sent to the Salesforce platform event\.

## Step 5: Clean up your resources<a name="cleanup"></a>

You can now delete the resources that you created for this tutorial, unless you want to retain them\. By deleting AWS resources that you are no longer using, you prevent unnecessary charges to your AWS account\.

**To delete the EventBridge Connections\(s\)**

1. Open the [API destination page](https://console.aws.amazon.com/events/home#/apidestinations) of the EventBridge console\.

1. Choose the **Connections** tab\.

1. Select the Connection\(s\) you created\.

1. Choose **Delete**\.

1. Enter the name of the connection and choose **Delete**\.

**To delete the EventBridge API destination\(s\)**

1. Open the [API destination page](https://console.aws.amazon.com/events/home#/apidestinations) of the EventBridge console\.

1. Select the API destinations\(s\) you created\.

1. Choose **Delete**\.

1. Enter the name of the API destination and choose **Delete**\.

**To delete the EventBridge rule\(s\)**

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rules) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.