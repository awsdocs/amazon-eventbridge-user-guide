# Amazon EventBridge code bindings<a name="eb-schema-code-bindings"></a>

You can generate code bindings for event [schemas](eb-schema.md) to speed up development in Golang, Java, Python, and TypeScript\. Code bindings are available for AWS service events, schemas you [create](eb-schema-create.md), and for schemas you [generate](eb-schema-create.md#eb-schemas-infer) based on [events](eb-events.md) on an [event bus](eb-event-bus.md)\. You can generate code bindings for a schema by using the EventBridge console, the EventBridge [ Schema Registry API](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/index.html), or in your IDE with an AWS toolkit\.

**To generate code bindings from an EventBridge schema**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. Find a schema that you want code bindings for, either by browsing the schema registries, or by searching for a schema\.

1. Select the schema name\.\.

1. On the **Schema details** page, in the **Version** section, choose **Download code bindings**\.

1. On the **Download code bindings** page, select the language of the code bindings you want to download\.

1. Select **Download**\.

   It may take a few seconds for your download to begin\. The downloaded file is a zip file of code bindings for the language you selected\.