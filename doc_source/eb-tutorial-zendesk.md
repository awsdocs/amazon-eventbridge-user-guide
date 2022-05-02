# Tutorial: Create a connection to Zendesk as an API destination<a name="eb-tutorial-zendesk"></a>

You can use EventBridge to route [events](eb-events.md) to third\-party services like [https://www.zendesk.com/](https://www.zendesk.com/)\.

In this tutorial, you'll use the EventBridge console to create a connection to Zendesk, an [API destination](eb-api-destinations.md) that points to Zendesk, and a [rule](eb-rules.md) to route events to Zendesk\. 

**Topics**
+ [Prerequisites](#eb-zd-prereqs)
+ [Step 1: Create connection](#eb-zd-create-connection)
+ [Step 2: Create API destination](#eb-zd-api-destination)
+ [Step 3: Create rule](#eb-zd-create-rule)
+ [Step 4: Test the rule](#eb-zd-test-rule)
+ [Step 5: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-zd-prereqs"></a>

To complete this tutorial, you'll need the following resources:
+ A [Zendesk account](https://www.zendesk.com/register/#step-1)\.
+ An EventBridge\-enabled [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/Welcome.html) bucket\.

## Step 1: Create connection<a name="eb-zd-create-connection"></a>

To send events to Zendesk, you'll first have to establish a connection to the Zendesk API\.

**To create the connection**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose the **Connections** tab, and then choose **Create connection**\.

1. Enter a name and description for the connection\. For example, enter **Zendesk** for the name, and **Connection to Zendesk API** for the description\.

1. For **Authorization type**, choose **Basic \(Username/Password\)**\.

1. For **Username**, enter your Zendesk username\.

1. For **Password**, enter your Zendesk password\.

1. Choose **Create**\.

## Step 2: Create API destination<a name="eb-zd-api-destination"></a>

Now that you've created the connection, you'll next create the API destination to use as the [target](eb-targets.md) of the rule\.

**To create the API Destination**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose **Create API destination**\.

1. Enter a name and description for the API destination\. For example, enter **ZendeskAD** for the name, and **Zendesk API destination** for the description\.

1. For **API destination endpoint**, enter **https://*your\-subdomain*\.zendesk\.com/api/v2/tickets\.json**, where *your\-subdomain* is the subdomain associated with your Zendesk account\.

1. For **HTTP method**, choose **POST**\.

1. For **Invocation rate limit**, enter **10**\.

1. For **Connection**, choose **Use an existing connection** and choose the `Zendesk` connection you created in step 1\.

1. Choose **Create**\.

## Step 3: Create rule<a name="eb-zd-create-rule"></a>

Next, create a rule to send events to Zendesk when an Amazon S3 object is created\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, enter **ZendeskRule** for the name, and **Rule to send events to Zendesk when S3 objects are created** for the description\.

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

1. For **API destination**, choose **Use an existing API destination**, and then choose the `ZendeskAD` destination you created in step 2\.

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

## Step 4: Test the rule<a name="eb-zd-test-rule"></a>

To test your rule, create an [Amazon S3 object](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html) by uploading a file to an EventBridge\-enabled bucket\. When the event matches the rule, EventBridge will call the [Zendesk Create Ticket API](https://developer.zendesk.com/rest_api/docs/support/tickets#create-ticket)\. The new ticket will appear in the Zendesk dashboard\.

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

1. Open the [Rules page](https://console.aws.amazon.com/events/home#/rule) of the EventBridge console\.

1. Select the rule\(s\) that you created\.

1. Choose **Delete**\.

1. Choose **Delete**\.