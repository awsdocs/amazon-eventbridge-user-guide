# Delete an EventBridge scheduled rule using an AWS SDK<a name="example_eventbridge_DeleteRule_section"></a>

The following code examples show how to delete an Amazon EventBridge scheduled rule\.

------
#### [ Java ]

**SDK for Java 2\.x**  
  

```
    public static void deleteEBRule(EventBridgeClient eventBrClient, String ruleName) {

        try {

           // Disable the rule - an Enabled Rule cannot be deleted
            DisableRuleRequest disableRuleRequest = DisableRuleRequest.builder()
                    .name(ruleName)
                    .eventBusName("default")
                    .build();

            eventBrClient.disableRule(disableRuleRequest);

            DeleteRuleRequest ruleRequest = DeleteRuleRequest.builder()
                    .name(ruleName)
                    .eventBusName("default")
                    .build();

            eventBrClient.deleteRule(ruleRequest);
           System.out.println("Rule "+ruleName + " was successfully deleted!");

        } catch (EventBridgeException e) {

            System.err.println(e.awsErrorDetails().errorMessage());
            System.exit(1);
        }
    }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/eventbridge#readme)\. 
+  For API details, see [DeleteRule](https://docs.aws.amazon.com/goto/SdkForJavaV2/eventbridge-2015-10-07/DeleteRule) in *AWS SDK for Java 2\.x API Reference*\. 

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
  

```
suspend fun deleteEBRule(ruleName: String) {

        val request =  DisableRuleRequest {
            name = ruleName
            eventBusName = "default"
        }

        EventBridgeClient { region = "us-west-2" }.use { eventBrClient ->
            eventBrClient.disableRule(request)
            val ruleRequest = DeleteRuleRequest {
                name = ruleName
                eventBusName = "default"
            }

            eventBrClient.deleteRule(ruleRequest)
            println("Rule $ruleName was successfully deleted!")
        }
}
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/services/eventbridge#code-examples)\. 
+  For API details, see [DeleteRule](https://github.com/awslabs/aws-sdk-kotlin#generating-api-documentation) in *AWS SDK for Kotlin API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using EventBridge with an AWS SDK](sdk-general-information-section.md)\.