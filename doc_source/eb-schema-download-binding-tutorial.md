# Tutorial: Download code bindings for events using the EventBridge schema registry<a name="eb-schema-download-binding-tutorial"></a>

You can generate [code bindings](eb-schema-code-bindings.md) for [event schemas](eb-schema.md) to speed development for Golang, Java, Python, and TypeScript\. You can get code bindings for existing AWS services, schemas you create, and for schemas you generate based on [events](eb-events.md) on an [event bus](eb-event-bus.md)\. You can generate code bindings for a schema using one of the following:
+ EventBridge console
+ EventBridge schema registry API
+ Your IDE with an AWS toolkit

In this tutorial you generate and download code bindings from an EventBridge schema for the events of an AWS service\.

**To generate code bindings from an EventBridge schema**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. Select the **AWS event schema registry** tab\.

1. Find the schema for the AWS service that you would like code bindings for, either by browsing through the schema registry, or by searching for a schema\.

1. Select the schema name\.

1. On the **Schema details** page, in the **Version** section, select **Download code bindings**\.

1. On the **Download code bindings** page, select the language of the code bindings you want to download\.

1. Select **Download**\.

   It may take a few seconds for your download to begin\. The download file will be a \.zip file of code bindings for the language you selected\.

1. Unzip the downloaded file and add it to your project\.

   The downloaded package contains a README file that explains how to configure the package's dependencies in various frameworks\.

Use these code bindings in your own code to help quickly build applications using this EventBridge event\.