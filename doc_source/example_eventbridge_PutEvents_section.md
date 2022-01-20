# Send EventBridge events using an AWS SDK<a name="example_eventbridge_PutEvents_section"></a>

The following code examples show how to send Amazon EventBridge events\.

------
#### [ C\+\+ ]

**SDK for C\+\+**  
Include the required files\.  

```
#include <aws/core/Aws.h>
#include <aws/events/EventBridgeClient.h>
#include <aws/events/model/PutEventsRequest.h>
#include <aws/events/model/PutEventsResult.h>
#include <aws/core/utils/Outcome.h>
#include <iostream>
```
Send the event\.  

```
        Aws::CloudWatchEvents::EventBridgeClient cwe;

        Aws::CloudWatchEvents::Model::PutEventsRequestEntry event_entry;
        event_entry.SetDetail(MakeDetails(event_key, event_value));
        event_entry.SetDetailType("sampleSubmitted");
        event_entry.AddResources(resource_arn);
        event_entry.SetSource("aws-sdk-cpp-cloudwatch-example");

        Aws::CloudWatchEvents::Model::PutEventsRequest request;
        request.AddEntries(event_entry);

        auto outcome = cwe.PutEvents(request);
        if (!outcome.IsSuccess())
        {
            std::cout << "Failed to post CloudWatch event: " <<
                outcome.GetError().GetMessage() << std::endl;
        }
        else
        {
            std::cout << "Successfully posted CloudWatch event" << std::endl;
        }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/eventbridge#code-examples)\. 
+  For API details, see [PutEvents](https://docs.aws.amazon.com/goto/SdkForCpp/eventbridge-2015-10-07/PutEvents) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
  

```
    public static void putEBEvents(EventBridgeClient eventBrClient, String resourceArn, String resourceArn2 ) {

        try {
            // Populate a List with the resource ARN values
            List<String> resources = new ArrayList<String>();
            resources.add(resourceArn);
            resources.add(resourceArn2);

            PutEventsRequestEntry reqEntry = PutEventsRequestEntry.builder()
                    .resources(resources)
                    .source("com.mycompany.myapp")
                    .detailType("myDetailType")
                    .detail("{ \"key1\": \"value1\", \"key2\": \"value2\" }")
                    .build();

            // Add the PutEventsRequestEntry to a list
            List<PutEventsRequestEntry> list = new ArrayList<PutEventsRequestEntry>();
            list.add(reqEntry);

            PutEventsRequest eventsRequest = PutEventsRequest.builder()
                    .entries(reqEntry)
                    .build();

            PutEventsResponse result = eventBrClient.putEvents(eventsRequest);

            for (PutEventsResultEntry resultEntry : result.entries()) {
                if (resultEntry.eventId() != null) {
                    System.out.println("Event Id: " + resultEntry.eventId());
                } else {
                    System.out.println("Injection failed with Error Code: " + resultEntry.errorCode());
                }
            }

        } catch (EventBridgeException e) {

            System.err.println(e.awsErrorDetails().errorMessage());
            System.exit(1);
        }
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/eventbridge#readme)\. 
+  For API details, see [PutEvents](https://docs.aws.amazon.com/goto/SdkForJavaV2/eventbridge-2015-10-07/PutEvents) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
Create the client in a separate module and export it\.  

```
import { EventBridgeClient } from "@aws-sdk/client-eventbridge";
// Set the AWS Region.
const REGION = "REGION"; //e.g. "us-east-1"
// Create an Amazon EventBridge service client object.
export const ebClient = new EventBridgeClient({ region: REGION });
```
Import the SDK and client modules and call the API\.  

```
// Import required AWS SDK clients and commands for Node.js.
import { PutEventsCommand } from "@aws-sdk/client-eventbridge";
import { ebClient } from "./libs/eventBridgeClient.js";

// Set the parameters.
export const params = {
  Entries: [
    {
      Detail: '{ "key1": "value1", "key2": "value2" }',
      DetailType: "appRequestSubmitted",
      Resources: [
        "RESOURCE_ARN", //RESOURCE_ARN
      ],
      Source: "com.company.app",
    },
  ],
};

export const run = async () => {
  try {
    const data = await ebClient.send(new PutEventsCommand(params));
    console.log("Success, event sent; requestID:", data);
    return data; // For unit tests.
  } catch (err) {
    console.log("Error", err);
  }
};
// Uncomment this line to run execution within this file.
// run();
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/eventbridge#code-examples)\. 
+  For API details, see [PutEvents](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-eventbridge/classes/puteventscommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region
AWS.config.update({region: 'REGION'});

// Create CloudWatchEvents service object
var ebevents = new AWS.EventBridge({apiVersion: '2015-10-07'});

var params = {
  Entries: [
    {
      Detail: '{ \"key1\": \"value1\", \"key2\": \"value2\" }',
      DetailType: 'appRequestSubmitted',
      Resources: [
        'RESOURCE_ARN',
      ],
      Source: 'com.company.app'
    }
  ]
};

ebevents.putEvents(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data.Entries);
  }
});
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/eventbridge#code-examples)\. 
+  For API details, see [PutEvents](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/eventbridge-2015-10-07/PutEvents) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

```
suspend fun putEBEvents(resourceArn: String, resourceArn2: String) {

            // Populate a List with the resource ARN values.
            val resourcesOb  = mutableListOf<String>()
            resourcesOb.add(resourceArn)
            resourcesOb.add(resourceArn2)

            val reqEntry = PutEventsRequestEntry {
                resources = resourcesOb
                source = "com.mycompany.myapp"
                detailType = "myDetailType"
                detail = "{ \"key1\": \"value1\", \"key2\": \"value2\" }"
            }

            val request = PutEventsRequest {
                entries = listOf(reqEntry)
            }

            EventBridgeClient { region = "us-west-2" }.use { eventBrClient ->
              val response = eventBrClient.putEvents(request)
              response.entries?.forEach { resultEntry ->

               if (resultEntry.eventId != null) {
                    println("Event Id is ${resultEntry.eventId}")
                } else {
                    println("Injection failed with Error Code ${resultEntry.errorCode}")
                }
            }
        }
 }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/eventbridge#code-examples)\. 
+  For API details, see [PutEvents](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using EventBridge with an AWS SDK](sdk-general-information-section.md)\.