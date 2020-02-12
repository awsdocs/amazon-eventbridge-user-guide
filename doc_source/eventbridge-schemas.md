# Amazon EventBridge Schema Registry<a name="eventbridge-schemas"></a>

The EventBridge Schema Registry allows you to discover, create, and manage OpenAPI schemas for events on EventBridge\. You can find schemas for existing AWS services, create and upload custom schemas, or generate a schema based on events on an event bus\. For all schemas in EventBridge you can generate and download code bindings to help quickly build applications that use those events\.

Schemas are available for the events of all AWS services on Amazon EventBridge\. You can also create or upload schemas, or automatically infer schemas directly from events on an event bus\. Once you have found or created a schema for an event, you can download code bindings for popular programming languages\. You can browse, search, create, upload, and generate code bindings for schemas\. You can manage schemas from the Amazon EventBridge console, using the API, or directly in your IDE using the AWS Toolkits\. You can quickly build serverless apps that use events using the AWS Serverless Application Model\.

For information about using the EventBridge Schema Registry with the API, or through Amazon CloudFront, see the following\.
+ [Amazon EventBridge Schema Registry API Reference](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/index.html)
+ [EventSchemas Resource Type Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_EventSchemas.html) in AWS CloudFormation

**Topics**
+ [Discover Existing AWS Event Schemas](#eventbridge-schemas-aws)
+ [Schema Registries](#eventbridge-schemas-registries)
+ [Upload or Create Schemas](#eventbridge-schemas-create)
+ [Generate a Schema from Event JSON](#eventbridge-schemas-infer-from-json)
+ [Generate a Schema Based on Events on an Event Bus](#eventbridge-schemas-infer)
+ [Generate Code Bindings for EventBridge Schemas](#eventbridge-schemas-bindings)
+ [EventBridge Schema Registry Integration with AWS Toolkits](#eventbridge-schemas-toolkits)

## Discover Existing AWS Event Schemas<a name="eventbridge-schemas-aws"></a>

Amazon EventBridge includes schemas for all AWS services on EventBridge\. You can search or browse these schemas in the Amazon EventBridge console, or by using API actions \(see [https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-search.html](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-search.html)\)\.

**To find schemas for AWS services**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. On the **Schemas** page, select **AWS event schema registry**\.

   The first page of available schemas is displayed\.

1. To search for a schema, enter a search term in the **Search AWS event schemas\.**

   A search returns matches for both the name and contents of the available schemas, and will display which versions of the schema it was found in\.

1. Open an event schema by selecting the name of the schema\.

The **Schema details** page is displayed\.

## Schema Registries<a name="eventbridge-schemas-registries"></a>

Schema registries are containers for Schemas\. Registries collect and organize schemas so that your schemas are in logical groups\. You can view **All schemas,** or the built\-in schemas, **AWS event schema registry** and **Discovered schema registry**\. You can also create custom registries to collect and organize the schemas you create or upload\.

**To create a custom registry**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then **Create registry**\.

1. On the **Registry details** page enter a **Name**\.

1. Optionally, enter a description for your new registry

1. Choose **Create**\.

The custom registry is created\. You can now see it on the main **Schemas** page\.

You can select **Create custom schema** from your new registry, or select that registry when you are creating a new schema\.

To create a registry using the Amazon EventBridge Schema Registry API, use the [https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname.html#v1-registries-name-registryname-http-methods](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname.html#v1-registries-name-registryname-http-methods) API action\.

## Upload or Create Schemas<a name="eventbridge-schemas-create"></a>

Schemas are defined using JSON files, using the [OpenAPI Specification](https://swagger.io/docs/specification/)\. You can create or upload your own event schemas in EventBridge using this specification\. You can download a template, or you can edit a template directly in the EventBridge console\. 

**To create a schema from a downloaded template**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schema registry**\.

1. In the **Getting started** section under **Schema template**, choose **Download**\.

1. Alternatively, you can download the JSON from the following code example\.

   ```
   {
       "openapi": "3.0.0",
       "info": {
         "version": "1.0.0",
         "title": "Event"
       },
       "paths": {},
       "components": {
         "schemas": {
           "Event": {
             "type": "object",
             "properties": {
               "ordinal": {
                 "type": "number",
                 "format": "int64"
               },
               "name": {
                 "type": "string"
               },
               "price": {
                 "type": "number",
                 "format": "double"
               },
               "address": {
                 "type": "string"
               },
               "comments": {
                 "type": "array",
                 "items": {
                   "type": "string"
                 }
               },
               "created_at": {
                 "type": "string",
                 "format": "date-time"
               }
             }
           }
         }
       }
     }
   ```

1. Edit the template so that the schema matches your events\. For more information on events, see [Events and Event Patterns in EventBridge](eventbridge-and-event-patterns.md)\.

**To upload a schema file**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then **Create schema**\.

1. Optionally, select or create a schema registry\.

1. Under **Schema details** enter a name for your schema\.

1. Optionally, enter a description for the schema you created\.

1. With the **Create** tab selected, either drag your schema file to the text box, or paste the schema source\.

1. Select **Create**\.

**To edit a template directly in the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then **Create schema**\.

1. Optionally, select or create a schema registry\.

1. Under **Schema details** enter a name for your schema\.

1. Optionally, enter a description for the schema you created\.

1. With the **Create** tab selected, choose **Load template**\.

1. Edit the template so that the schema matches your events\. For more information on events, see [Events and Event Patterns in EventBridge](eventbridge-and-event-patterns.md)\.

1. Select **Create**\.

To create a schema using the EventBridge Schema Registry API, use the [https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-name-schemaname.html#v1-registries-nam](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-name-schemaname.html#v1-registries-nam) API action\.

## Generate a Schema from Event JSON<a name="eventbridge-schemas-infer-from-json"></a>

With the JSON of an event, you can automatically generate a schema for those types of events\. Given existing event code, you can quickly generate a custom schema\. Once the schema has been generated, you can download code bindings to help create applications for those types of events\.

**To generate an EventBridge schema based on event JSON**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then **Create schema**\.

1. Optionally, select or create a schema registry\.

1. Under **Schema details** enter a name for your schema\.

1. Optionally, enter a description for the schema you created\.

1. Select **Discover from JSON**

1. In the text box under **JSON**, paste or drag the JSON source of an event\.

   As an example, you could paste in the source from this AWS Step Functions event for a failed execution\.

   ```
   {
       "version": "0",
       "id": "315c1398-40ff-a850-213b-158f73e60175",
       "detail-type": "Step Functions Execution Status Change",
       "source": "aws.states",
       "account": "012345678912",
       "time": "2019-02-26T19:42:21Z",
       "region": "us-east-1",
       "resources": [
         "arn:aws:states:us-east-1:012345678912:execution:state-machine-name:execution-name"
       ],
       "detail": {
           "executionArn": "arn:aws:states:us-east-1:012345678912:execution:state-machine-name:execution-name",
           "stateMachineArn": "arn:aws:states:us-east-1:012345678912:stateMachine:state-machine",
           "name": "execution-name",
           "status": "FAILED",
           "startDate": 1551225146847,
           "stopDate": 1551225151881,
           "input": "{}",
           "output": null
       }
   }
   ```

1. Select **Discover schema**\.

1. EventBridge will generate an OpenAPI schema for the event\. For example, the following is the generated schema for the event you pasted in\.

   ```
   {
     "openapi": "3.0.0",
     "info": {
       "version": "1.0.0",
       "title": "StepFunctionsExecutionStatusChange"
     },
     "paths": {},
     "components": {
       "schemas": {
         "AWSEvent": {
           "type": "object",
           "required": ["detail-type", "resources", "detail", "id", "source", "time", "region", "version", "account"],
           "x-amazon-events-detail-type": "Step Functions Execution Status Change",
           "x-amazon-events-source": "aws.states",
           "properties": {
             "detail": {
               "$ref": "#/components/schemas/StepFunctionsExecutionStatusChange"
             },
             "account": {
               "type": "string"
             },
             "detail-type": {
               "type": "string"
             },
             "id": {
               "type": "string"
             },
             "region": {
               "type": "string"
             },
             "resources": {
               "type": "array",
               "items": {
                 "type": "string"
               }
             },
             "source": {
               "type": "string"
             },
             "time": {
               "type": "string",
               "format": "date-time"
             },
             "version": {
               "type": "string"
             }
           }
         },
         "StepFunctionsExecutionStatusChange": {
           "type": "object",
           "required": ["output", "input", "executionArn", "name", "stateMachineArn", "startDate", "stopDate", "status"],
           "properties": {
             "executionArn": {
               "type": "string"
             },
             "input": {
               "type": "string"
             },
             "name": {
               "type": "string"
             },
             "output": {},
             "startDate": {
               "type": "integer",
               "format": "int64"
             },
             "stateMachineArn": {
               "type": "string"
             },
             "status": {
               "type": "string"
             },
             "stopDate": {
               "type": "integer",
               "format": "int64"
             }
           }
         }
       }
     }
   }
   ```

1. Once the schema has been generated, select **Create**\.

## Generate a Schema Based on Events on an Event Bus<a name="eventbridge-schemas-infer"></a>

Amazon EventBridge can infer schemas based on events on an event bus\. Enabling event discovery on an event bus will generate schemas for events on that bus\. 

**Note**  
Enabling event discovery on an event bus may incur a cost\. The first five million ingested events in each month is free\.

**To enable schema discovery on an event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. To enable discovery on the **Default event bus**, select **Start discovery**\.

1. To enable discovery on a **Custom event bus**, select the radio button for the custom event bus and choose **Start Discovery**\.

Discovered schemas will show up in the **Discovered schemas registry** on the **Schemas** page\. Changes to the contents of events on that bus will create new versions of the related EventBridge schema in the **Discovered schemas registry**\.

## Generate Code Bindings for EventBridge Schemas<a name="eventbridge-schemas-bindings"></a>

You can generate code bindings for event schemas to speed development for Java, Python, and TypeScript\. You can get code bindings for existing AWS services, schemas you create, and for schemas you generate based on events on an event bus\. You can generate code bindings for a schema using the EventBridge console, the EventBridge Schema Registry API, and directly in your IDE with the AWS Toolkit\.

You must enable discovery on an event bus to generate code bindings\.

**To generate code bindings from an EventBridge schema**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas**\.

1. Find a schema that you would like code bindings for, either by looking through the schema registries, or by searching for a schema\.

1. Select the schema name to display the **Schema details** page\.

1. In the **Version** section, select **Download code bindings**\.

1. On the **Download code bindings** page, select the language of the code bindings you want to download\.

1. Select **Download**\.

   It may take a few seconds for your download to begin\. The download file will be a zip file of code bindings for the language you selected\.

## EventBridge Schema Registry Integration with AWS Toolkits<a name="eventbridge-schemas-toolkits"></a>

EventBridge Schema Registry integrates with some AWS Toolkits, letting you browse or search for schemas and download code bindings for schemas directly in your IDE\. 

For more information, see the following AWS Toolkit documentation links\.
+ [AWS Toolkit for JetBrains](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/eventbridge-schemas.html)
+ [AWS Toolkit for VS Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/working-with-aws.html)