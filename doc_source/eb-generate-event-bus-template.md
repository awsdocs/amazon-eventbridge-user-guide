# Generate an AWS CloudFormation template from an Amazon EventBridge event bus<a name="eb-generate-event-bus-template"></a>

AWS CloudFormation enables you to configure and manage your AWS resources across accounts and regions in a centralized and repeatable manner by treating infrastructure as code\. AWS CloudFormation does this by letting you create *templates*, which define the resources you want to provision and manage\.

EventBridge enables you to generate templates from the existing event buses in your account, as an aid to help you jumpstart developing AWS CloudFormation templates\. In addition, EventBridge provides the option of including the rules associated with that event bus in your template\. You can then use these templates as the basis for [creating stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) of resources under AWS CloudFormation management\.

For more information on AWS CloudFormation see [*The AWS CloudFormation User Guide*\.](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

**Note**  
EventBridge does not include [managed rules](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) in the generated template\.

You can also [generate a template from one or more rules contained in a selected event bus](https://docs.aws.amazon.com/eventbridge/latest/userguide/rule-create-template.html)\.

**To generate an AWS CloudFormation template from an event bus**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Event buses**\.

1. Choose the event bus from which you want to generate a AWS CloudFormation template\.

1. From the **Actions** menu, choose **CloudFormation Template**, and then choose which format you want EventBridge to generate the template in: **JSON** or **YAML**\.

   EventBridge displays the template, generated in the selected format\. By default, all rules associated with the event bus are included in the template\.

   1. To generate the template without including rules, deselect **Include rules on this EventBus**\.

1. EventBridge gives you the option of downloading the template file, or copying the template to the clipboard\.
   + To download the template file, choose **Download**\.
   + To copy the template to the clipboard, choose **Copy**\.

1. To exit the template, choose **Cancel**\. 

Once you've customized your AWS CloudFormation template as necessary for your use case, you can use it to [create stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in AWS CloudFormation\.

## Considerations when using AWS CloudFormation templates generated from Amazon EventBridge<a name="eb-generate-event-bus-template-considerations"></a>

Consider the following factors when using a AWS CloudFormation template you generated from an event bus:
+ EventBridge does not include any passwords in the generate template\.

  You can edit the template to include [template parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) that enable users to specify passwords or other sensitive information when using the template to create or update a AWS CloudFormation stack\.

  In addition, users can use Secrets Manager to create a secret in the desired region and then edit the generated template to employ [dynamic parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html#dynamic-references-secretsmanager)\.
+ Targets in the generated template remain exactly as they were specified in the original event bus\. This can lead to cross\-region issues if you do not appropriately edit the template before using it to create stacks in other regions\.

  Additionally, the generated template will not create the downstream targets automatically\.