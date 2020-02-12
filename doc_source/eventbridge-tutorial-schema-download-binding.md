# Tutorial: Download Code Bindings for Events using the EventBridge Schema Registry<a name="eventbridge-tutorial-schema-download-binding"></a>

You can generate code bindings for event schemas to speed development for Java, Python, and TypeScript\. You can get code bindings for existing AWS services, schemas you create, and for schemas you generate based on events on an event bus\. You can generate code bindings for a schema using the EventBridge console, the EventBridge Schema Registry API, and directly in your IDE with the AWS Toolkit\.

In this tutorial you will generate and download code bindings from an EventBridge Schema for the events of an AWS service\.

**To generate code bindings from an EventBridge schema**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. Select the **AWS event schema registry** tab\.

1. Find a schema for an AWS service that you would like code bindings for, either by browsing through the schema registry, or by searching for a schema\.

1. Select the schema name to display the **Schema details** page\.

1. In the **Version** section, select **Download code bindings**\.

1. On the **Download code bindings** page, select the language of the code bindings you want to download\.

1. Select **Download**\.

   It may take a few seconds for your download to begin\. The download file will be a zip file of code bindings for the language you selected\.

Use these code bindings in your own code to help quickly build applications using this EventBridge event\.