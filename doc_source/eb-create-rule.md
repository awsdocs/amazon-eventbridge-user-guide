# Creating Amazon EventBridge rules that react to events<a name="eb-create-rule"></a>

To take action on [events](eb-events.md) received by Amazon EventBridge, you can create [rules](eb-rules.md)\. When an event matches the [event pattern](eb-event-patterns.md) defined in your rule, EventBridge sends the event to the specified [target](eb-targets.md) and triggers the action defined in the rule\.



## Define the rule<a name="eb-create-rule-define"></a>

First, enter a name and description for your rule to identify it\. You must also define the event bus where your rule looks for events to match to an event pattern\.

**To define the rule detail**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a **Name** and, optionally, a **Description** for the rule\.

   A rule can't have the same name as another rule in the same AWS Region and on the same event bus\.

1. For **Event bus**, choose the event bus to associate with this rule\. If you want this rule to match events that come from your account, select **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account’s default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

## Build the event pattern<a name="eb-create-rule-event-pattern"></a>

Next, build the event pattern\. To do this, specify the event source, choose the basis for the event pattern, and define the attributes and values to match on\. You can also generate the event pattern in JSON and test it against a sample event\.

**To build the event pattern**

1. For **Event source**, choose **AWS events or EventBridge partner events**\.

1. \(Optional\) In the **Sample events** section, choose a **Sample event type** if you want to test your event pattern against an example event\. The following sample event types are available:
   + **AWS events **– Select from events emitted from supported AWS services\.
   + **EventBridge partner events** – Select from events emitted from third\-party services that support EventBridge, such as Salesforce\.
   + **Enter my own** – Enter your own event in JSON text\.

1. Choose a **Creation method**\. You can create an event pattern from an EventBridge schema or template, or you can create a custom event pattern\.
   + To use an existing EventBridge schema to create the event pattern, do the following:

     1. In the **Creation method** section, for **Method**, select **Use schema**\.

     1. In the **Event pattern** section, for **Schema type**, select **Select schema from Schema registry**\.

     1. For **Schema registry**, choose the dropdown box and enter the name of a schema registry, such as `aws.events`\. You can also select an option from the dropdown list that appears\.

     1. For **Schema**, choose the dropdown box and enter the name of the schema to use\. For example, `aws.s3@ObjectDeleted`\. You can also select an option from the dropdown list that appears\.

     1. In the **Models** section, choose the **Edit** button next to any attribute to open its properties\. Set the **Relationship** and **Value** fields as needed, then choose **Set** to save the attribute\.
**Note**  
For information about an attribute's definition, choose the **Info** icon next to the attribute's name\. For a reference on how to set attribute properties in your event, open the **Note** section of the attribute properties dialog box\.  
To delete an attribute's properties, choose the **Edit** button for that attribute, then choose **Clear**\.

     1. Choose **Generate event pattern in JSON** to generate and validate your event pattern as JSON text\. Then, choose any of the following options:
        + **Copy** – Copy the event pattern to your device's clipboard\.
        + **Prettify** – Makes the JSON text easier to read by adding line breaks, tabs, and spaces\.
        + **Test pattern** – Tests the JSON\-formatted pattern against the sample event from the **Sample events** section\.
   + To write a custom schema and convert it to an event pattern, do the following:

     1. In the **Creation method** section, for **Method**, choose **Use schema**\.

     1. In the **Event pattern** section, for **Schema type**, choose **Enter schema**\.

     1. Enter your schema into the text box\. You must format the schema as valid JSON text\.

     1. In the **Models** section, choose the **Edit** button next to any attribute to open its properties\. Set the **Relationship** and **Value** fields as needed, then choose **Set** to save the attribute\.
**Note**  
For information about an attribute's definition, choose the **Info** icon next to the attribute's name\. For a reference on how to set attribute properties in your event, open the **Note** section of the attribute properties dialog box\.  
To delete an attribute's properties, choose the **Edit** button for that attribute, then choose **Clear**\.

     1. Choose **Generate event pattern in JSON** to generate and validate your event pattern as JSON text\. Then, choose any of the following options:
        + **Copy** – Copy the event pattern to your device's clipboard\.
        + **Prettify** – Makes the JSON text easier to read by adding line breaks, tabs, and spaces\.
        + **Test pattern** – Tests the JSON\-formatted pattern against the sample event from the **Sample events** section\.
   + To write a custom event pattern in JSON format, do the following:

     1. In the **Creation method** section, for **Method**, choose **Custom pattern \(JSON editor\)**\.

     1. For **Event pattern**, enter your custom event pattern in JSON\-formatted text\. 

     1. After you create your pattern, choose any of the following options:
        + **Copy** – Copy the event pattern to your device's clipboard\.
        + **Prettify** – Makes the JSON text easier to read by adding line breaks, tabs, and spaces\.
        + **Event pattern form** – Opens the event pattern in Pattern Builder\. If the pattern can't be rendered in Pattern Builder as\-is, EventBridge warns you before it opens Pattern Builder\.
        + **Test pattern** – Tests the JSON\-formatted pattern against the sample event from the **Sample events** section\.

1. Choose **Next**\.

## Select targets<a name="eb-create-rule-target"></a>

Choose one or more targets to receive events that match the specified pattern\. Targets can include an EventBridge event bus, EventBridge API destinations, including SaaS partners such as Salesforce, or another AWS service\.

**To select targets**

1. For **Target type**, choose one of the following options:
   + To select an EventBridge event bus, select **EventBridge event bus**, then do the following:

     1. To use an event bus in the same AWS Region as this rule, select **Event bus in the same account and Region**\. Then, for **Event bus for target**, choose the dropdown box and enter the name of the event bus You can also select the event bus from the dropdown list\.

     1. To use an event bus in a different AWS Region or account as this rule, select **Event bus in a different account or Region**\. Then, for **Event bus as target**, enter the ARN of the event bus you want to use\.
   + To use an EventBridge API destination, select **EventBridge API destination**, then do the following:

     1. To use an existing API destination, select **Use an existing API destination**\. Then select an API destination from the dropdown list\.

     1. To create a new API destination, select **Create a new API destination**\. Then, provide the following details for the destination:
        + **Name** – Enter a name for the destination\. Names must be unique within your AWS account\. Names can have up to 64 characters\. Valid characters are **A\-Z**, **a\-z**, **0\-9**, and **\.** **\_** **\-** \(hyphen\)\.
        + \(Optional\) **Description** – Enter a description for the destination\. Descriptions can have up to 512 characters\.
        + **API destination endpoint** – The URL endpoint for the target\. The endpoint URL must start with **https**\. You can include the **\*** as a path parameter wildcard\. You can set path parameters from the target's `HttpParameters` attribute\.
        + **HTTP method** – Select the HTTP method used when you invoke the endpoint\.
        + \(Optional\) **Invocation rate limit per second** – Enter the maximum number of invocations accepted for each second for this destination\. This value must be greater than zero\. By default, this value is set to 300\.
        + **Connection** – To use an existing connection, select **Use an existing connection** and select the connection from the dropdown list\. To create a new connection for this destination select **Create a new connection**, then define the connection's **Name**, **Destination type**, and **Authorization type**\. You can also add an optional **Description** for this connection\.
   + To use an AWS service, select **AWS service**, then do the following:

     1. For **Select a target**, select an AWS service to use as the target\. Provide the information requested for the service you select\.
**Note**  
The fields displayed vary depending on the service selected\. For more information about available targets, see [Targets available in the EventBridge console](eb-targets.md#eb-console-targets)\.

1. For **Execution role**, do one of the following:
   + To create a new execution role for this rule, select **Create a new role for this specific resource**\. Then either enter a name for this execution role, or use the name generated by EventBridge\.
   + To use an existing execution role for this rule, select **Use existing role**\. Then select or enter the name of the execution role to use from the dropdown list\.

1. Choose **Next**\.

## Configure tags and review rule<a name="eb-create-rule-review"></a>

Finally, enter any desired tags for the rule, then review and create the rule\.

**To configure tags , and review and create the rule**

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](eb-tagging.md)\.

1. Choose **Next**\.

1. Review the details for the new rule\. To make changes to any section, choose the **Edit** button next to that section\.

   When satisfied with the rule details, choose **Create rule**\.