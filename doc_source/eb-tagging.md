# Amazon EventBridge tags<a name="eb-tagging"></a>

A *tag* is a custom attribute label that you or AWS assigns to an AWS resource\. In EventBridge, you can assign tags to [rule](eb-rules.md) and [event buses](eb-event-bus.md)\. Each resource can have a maximum of 50 tags\.

You use tags to identify and organize your AWS resources\. Many AWS services support tagging, so you can assign the same tag to resources from different services to indicate that the resources are related\. For example, you could assign the same tag to an EventBridge rule that you assign to an EC2 instance\.

You also use tags to track your AWS costs\. You activate these tags on the AWS Billing and Cost Management dashboard\. AWS uses the tags to categorize your costs and then deliver a monthly cost allocation report to you\. For more information, see [Use Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the [AWS Billing User Guide](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/)\.

A tag has two parts:
+ A *tag key*, for example, `CostCenter`, `Environment`, or `Project`\. 
  + Tag keys are case sensitive\. 
  + The maximum tag key length is 128 Unicode characters in UTF\-8\. 
  + For each resource, each tag key must be unique\.
  + Allowed characters are letters, numbers, spaces representable in UTF\-8, and the following characters: ***\. : \+ = @ \_ / \- \(hyphen\)***\.
  + The `aws:` prefix is prohibited for tags because it's reserved for AWS use\. You can't edit or delete tag keys or values with this prefix\. Tags with this prefix don't count against your tags per resource limit\.
+ An optional *tag value* field, for example, `111122223333` or `Production`\. 
  + Each tag key can have only one value\. 
  + Tag values are case sensitive\. 
  + Omitting the tag value is the same as using an empty string\. 
  + The maximum tag value length is 256 Unicode characters in UTF\-8\.
  + Allowed characters are letters, numbers, spaces representable in UTF\-8, and the following characters: ***\. : \+ = @ \_ / \- \(hyphen\)***\.

**Tip**  
As a best practice, decide on a strategy for capitalizing tags and consistently implement that strategy across all resource types\. For example, decide whether to use `Costcenter`, `costcenter`, or `CostCenter` and then use the same convention for all tags\. 

You can use the EventBridge console, the EventBridge API, or the AWS CLI to add, edit, or delete tags\. For more information, see the following:
+ [TagResource](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_TagResource.html), [UntagResource](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_UntagResource.html), and [ListTagsForResource](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_ListTagsForResource.html) in the *Amazon CloudWatch Events API Reference*
+ [tag\-resource](https://docs.aws.amazon.com/cli/latest/reference/events/tag-resource.html), [untag\-resource](https://docs.aws.amazon.com/cli/latest/reference/events/untag-resource.html), and [list\-tags\-for\-resource](https://docs.aws.amazon.com/cli/latest/reference/events/list-tags-for-resource.html) in the *Amazon CloudWatch CLI Reference*
+ [Working with Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *Resource Groups User Guide*