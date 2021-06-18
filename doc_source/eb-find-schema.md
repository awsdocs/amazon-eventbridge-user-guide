# Finding an Amazon EventBridge schema<a name="eb-find-schema"></a>

EventBridge includes [schemas](eb-schema.md) for all AWS services that generate events\. You can find these schemas in the EventBridge console, or you can find them by using the API action [https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-search.html](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-search.html)\.

**To find schemas for AWS services in the EventBridge console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. On the **Schemas** page, select **AWS event schema registry**\.

   The first page of available schemas is displayed\.

1. To find a schema, in **Search AWS event schemas**, enter a search term**\.**

   A search returns matches for both the name and contents of the available schemas, and then displays which versions of the schema contain matches\.

1. Open an event schema by selecting the name of the schema\.