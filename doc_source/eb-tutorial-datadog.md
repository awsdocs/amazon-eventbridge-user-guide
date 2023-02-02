# Tutorial: Create a connection to Datadog as an API destination<a name="eb-tutorial-datadog"></a>

You can use EventBridge to route [events](eb-events.md) to third\-party services,such as [https://www.datadoghq.com/](https://www.datadoghq.com/)\.

In this tutorial, you'll use the EventBridge console to create a connection to Datadog, an [API destination](eb-api-destinations.md) that points to Datadog, and a [rule](eb-rules.md) to route events to Datadog\. 

**Topics**
+ [Prerequisites](#eb-dd-prereqs)
+ [Step 1: Create connection](#eb-dd-create-connection)
+ [Step 2: Create API destination](#eb-dd-api-destination)
+ [Step 3: Create rule](#eb-dd-create-rule)
+ [Step 4: Test the rule](#eb-dd-test-rule)
+ [Step 5: Clean up your resources](#cleanup)

## Prerequisites<a name="eb-dd-prereqs"></a>

To complete this tutorial, you'll need the following resources:
+ A [Datadog account](https://www.datadoghq.com/free-datadog-trial/)\.
+ A [Datadog API key](https://docs.datadoghq.com/account_management/api-app-keys/)\.
+ An EventBridge\-enabled [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/Welcome.html) bucket\.

## Step 1: Create connection<a name="eb-dd-create-connection"></a>

To send events to Datadog, you'll first have to establish a connection to the Datadog API\.

**To create the connection**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose the **Connections** tab, and then choose **Create connection**\.

1. Enter a name and description for the connection\. For example, enter **Datadog** as a name, and **Datadog API Connection** as a description\.

1. For **Authorization type**, choose **API key**\.

1. For **API key name**, enter **DD\-API\-KEY**\.

1. For **Value**, paste your Datadog secret API key\.

1. Choose **Create**\.

## Step 2: Create API destination<a name="eb-dd-api-destination"></a>

Now that you've created the connection, next you'll create the API destination to use as the [target](eb-targets.md) of the rule\.

**To create the API Destination**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **API destinations**\.

1. Choose **Create API destination**\.

1. Enter a name and description for the API destination\. For example, enter **DatadogAD** for the name, and **Datadog API Destination** for the description\.\.

1. For **API destination endpoint**, enter **https://http\-intake\.logs\.datadoghq\.com/api/v2/logs**\.

1. For **HTTP method**, choose **POST**\.

1. For **Invocation rate limit**, enter **300**\.

1. For **Connection**, choose **Use an existing connection** and choose the `Datadog` connection you created in step 1\.

1. Choose **Create**\.

## Step 3: Create rule<a name="eb-dd-create-rule"></a>

Next, you'll create a rule to send events to Datadog when an Amazon S3 object is created\.

**To create a rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, enter **DatadogRule** for the name, and **Rule to send events to Datadog for S3 object creation** for the description\.

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

1. For **API destination**, choose **Use an existing API destination**, and then choose the `DatadogAD` destination you created in step 2\.

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

To test your rule, create an [Amazon S3 object](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html) by uploading a file to an EventBridge\-enabled bucket\. The created object will be logged in the Datadog Logs console\.

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