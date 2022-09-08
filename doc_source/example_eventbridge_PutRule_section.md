# Create an EventBridge scheduled rule using an AWS SDK<a name="example_eventbridge_PutRule_section"></a>

The following code examples show how to create an Amazon EventBridge scheduled rule\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/eventbridge#code-examples)\. 
Include the required files\.  

```
#include <aws/core/Aws.h>
#include <aws/events/EventBridgeClient.h>
#include <aws/events/model/PutRuleRequest.h>
#include <aws/events/model/PutRuleResult.h>
#include <aws/core/utils/Outcome.h>
#include <iostream>
```
Create the rule\.  

```
        Aws::CloudWatchEvents::EventBridgeClient cwe;
        Aws::CloudWatchEvents::Model::PutRuleRequest request;
        request.SetName(rule_name);
        request.SetRoleArn(role_arn);
        request.SetScheduleExpression("rate(5 minutes)");
        request.SetState(Aws::CloudWatchEvents::Model::RuleState::ENABLED);

        auto outcome = cwe.PutRule(request);
        if (!outcome.IsSuccess())
        {
            std::cout << "Failed to create CloudWatch events rule " <<
                rule_name << ": " << outcome.GetError().GetMessage() <<
                std::endl;
        }
        else
        {
            std::cout << "Successfully created CloudWatch events rule " <<
                rule_name << " with resulting Arn " <<
                outcome.GetResult().GetRuleArn() << std::endl;
        }
```
+  For API details, see [PutRule](https://docs.aws.amazon.com/goto/SdkForCpp/eventbridge-2015-10-07/PutRule) in *AWS SDK for C\+\+ API Reference*\. 

------
#### [ Java ]

**SDK for Java 2\.x**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/eventbridge#readme)\. 
  

```
        public static void createEBRule(EventBridgeClient eventBrClient, String ruleName, String cronExpression) {

            try {
                PutRuleRequest ruleRequest = PutRuleRequest.builder()
                    .name(ruleName)
                    .eventBusName("default")
                    .scheduleExpression(cronExpression)
                    .state("ENABLED")
                    .description("A test rule that runs on a schedule created by the Java API")
                    .build();

                PutRuleResponse ruleResponse = eventBrClient.putRule(ruleRequest);
                System.out.println("The ARN of the new rule is "+ ruleResponse.ruleArn());

            } catch (EventBridgeException e) {
                System.err.println(e.awsErrorDetails().errorMessage());
                System.exit(1);
            }
        }
```
+  For API details, see [PutRule](https://docs.aws.amazon.com/goto/SdkForJavaV2/eventbridge-2015-10-07/PutRule) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/eventbridge#code-examples)\. 
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
import { PutRuleCommand } from "@aws-sdk/client-eventbridge";
import { ebClient } from "./libs/eventBridgeClient.js";

// Set the parameters.
export const params = {
  Name: "DEMO_EVENT",
  RoleArn: "IAM_ROLE_ARN", //IAM_ROLE_ARN
  ScheduleExpression: "rate(5 minutes)",
  State: "ENABLED",
};

export const run = async () => {
  try {
    const data = await ebClient.send(new PutRuleCommand(params));
    console.log("Success, scheduled rule created; Rule ARN:", data);
    return data; // For unit tests.
  } catch (err) {
    console.log("Error", err);
  }
};
// Uncomment this line to run execution within this file.
// run();
```
+  For API details, see [PutRule](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-eventbridge/classes/putrulecommand.html) in *AWS SDK for JavaScript API Reference*\. 

**SDK for JavaScript V2**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascript/example_code/eventbridge#code-examples)\. 
  

```
// Load the AWS SDK for Node.js
var AWS = require('aws-sdk');
// Set the region
AWS.config.update({region: 'REGION'});

// Create CloudWatchEvents service object
var ebevents = new AWS.EventBridge({apiVersion: '2015-10-07'});

var params = {
  Name: 'DEMO_EVENT',
  RoleArn: 'IAM_ROLE_ARN',
  ScheduleExpression: 'rate(5 minutes)',
  State: 'ENABLED'
};

ebevents.putRule(params, function(err, data) {
  if (err) {
    console.log("Error", err);
  } else {
    console.log("Success", data.RuleArn);
  }
});
```
+  For API details, see [PutRule](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/eventbridge-2015-10-07/PutRule) in *AWS SDK for JavaScript API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/eventbridge#code-examples)\. 
  

```
suspend fun createScRule(ruleName: String?, cronExpression: String?) {
    val ruleRequest = PutRuleRequest {
        name = ruleName
        eventBusName = "default"
        scheduleExpression = cronExpression
        state = RuleState.Enabled
        description = "A test rule that runs on a schedule created by the Kotlin API"
    }

    EventBridgeClient { region = "us-west-2" }.use { eventBrClient ->
        val ruleResponse = eventBrClient.putRule(ruleRequest)
        println("The ARN of the new rule is ${ruleResponse.ruleArn}")
    }
}
```
+  For API details, see [PutRule](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using EventBridge with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.