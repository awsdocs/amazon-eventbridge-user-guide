# Creating an Amazon EventBridge schema<a name="eb-schema-create"></a>

You create schemas by using JSON files with either the [OpenAPI Specification](https://swagger.io/specification/) or the [JSONSchema Draft4 specification](https://json-schema.org/specification-links.html#draft-4)\. You can create or upload your own schemas in EventBridge by using a template or generating a schema based on the JSON of an [event](eb-events.md)\. You can also infer the schema from events on an [event bus](eb-event-bus.md)\. To create a schema by using the EventBridge Schema Registry API, use the [https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-name-schemaname.html#v1-registries-nam](https://docs.aws.amazon.com/eventbridge/latest/schema-reference/v1-registries-name-registryname-schemas-name-schemaname.html#v1-registries-nam) API action\.

When you choose between OpenAPI 3 and JSONSchema Draft4 formats, consider the following differences:
+ JSONSchema format supports additional keywords that aren't supported in OpenAPI, such as `$schema, additionalItems`\.
+ There are minor differences in how keywords are handled, such as `type` and `format`\.
+ OpenAPI doesn't support JSONSchema Hyper\-Schema hyperlinks in JSON documents\.
+ Tools for OpenAPI tend to focus on build\-time, whereas tools for JSONSchema tend to focus on run\-time operations, such as client tools for schema validation\.

We recommend using JSONSchema format to implement client\-side validation so that events sent to EventBridge conform to the schema\. You can use JSONSchema to define a contract for valid JSON documents, and then use a [JSON schema validator](https://json-schema.org/implementations.html) before sending the associated events\.

After you have a new schema, you can download [code bindings](eb-schema-code-bindings.md) to help create applications for events with that schema\.

**Topics**
+ [Create a schema by using a template](#eb-schema-template)
+ [Edit a schema template directly in the console](#eb-schema-template-console)
+ [Create a schema from the JSON of an event](#eb-schemas-infer-from-json)
+ [Create a schema from events on an event bus](#eb-schemas-infer)

## Create a schema by using a template<a name="eb-schema-template"></a>

You can create a schema from a template or by editing a template directly in the EventBridge console\. To get the template, you download it from the console\. You can edit the template so that the schema matches your events\. Then upload your new template through the console\.

**Topics**

**To download the schema template**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schema registry**\.

1. In the **Getting started** section under **Schema template**, choose **Download**\.

Alternatively, you can copy the JSON template from the following code example\.

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



**To upload a schema template**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then choose **Create schema**\.

1. \(Optional\) Select or create a schema registry\.

1. Under **Schema details**, enter a name for your schema\.

1. \(Optional\) Enter a description for your schema\.

1. For **Schema type**, choose either **OpenAPI 3\.0** or **JSON Schema Draft 4**\.

1. On the **Create** tab, in the text box, either drag your schema file to the text box, or paste the schema source\.

1. Select **Create**\.

## Edit a schema template directly in the console<a name="eb-schema-template-console"></a>

**To edit a schema in the console**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then choose **Create schema**\.

1. \(Optional\) Select or create a schema registry\.

1. Under **Schema details**, enter a name for your schema\.

1. For **Schema type**, choose either **OpenAPI 3\.0** or **JSON Schema Draft 4**\.

1. \(Optional\) Enter a description for the schema to create\.

1. On the **Create** tab, choose **Load template**\.

1. In the text box, edit the template so that the schema matches your [events](eb-events.md)\. 

1. Select **Create**\.

## Create a schema from the JSON of an event<a name="eb-schemas-infer-from-json"></a>

If you have the JSON of an event, you can automatically create a schema for that type of event\.

**To create a schema based on the JSON of an event**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Schemas** and then choose **Create schema**\.

1. \(Optional\) Select or create a schema registry\.

1. Under **Schema details** enter a name for your schema\.

1. \(Optional\) Enter a description for the schema you created\.

1. For **Schema type**, choose **OpenAPI 3\.0**\.

   You can't use JSONSchema when you create a schema from the JSON of an event\.

1. Select **Discover from JSON**

1. In the text box under **JSON**, paste or drag the JSON source of an event\.

   For example, you could paste in the source from this AWS Step Functions event for a failed execution\.

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

1. Choose **Discover schema**\.

1. EventBridge generates an OpenAPI schema for the event\. For example, the following schema is generated for the preceding Step Functions event\.

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

1. After the schema has been generated, choose **Create**\.

## Create a schema from events on an event bus<a name="eb-schemas-infer"></a>

EventBridge can infer schemas by discovering events\. To infer schemas, you turn on event discovery on an event bus\. Schemas discovered by EventBridge appear in **Discovered schemas registry** on the **Schemas** page\.

If the contents of events on the event bus change, EventBridge creates new versions of the related EventBridge schema\.

**Note**  
Enabling event discovery on an event bus can incur a cost\. The first five million processed events in each month are free\.

**To enable schema discovery on an event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. Do one of the following:
   + To enable discovery on the **Default event bus**, choose **Start discovery**\.
   + To enable discovery on a **Custom event bus**, select the radio button for the custom event bus and then choose **Start discovery**\.



