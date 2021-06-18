# Amazon EventBridge permissions reference<a name="eb-permissions-reference"></a>





To specify an action in an EventBridge policy, use the `events:` prefix followed by the API operation name, as shown in the following example\.

```
"Action": "events:PutRule"
```

To specify multiple actions in a single statement, separate them with commas as follows\.

```
"Action": ["events:action1", "events:action2"]
```

To specify multiple actions, you can also use wildcards\. For example, you can specify all actions that begin with the word `"Put"` as follows\.

```
"Action": "events:Put*"
```

To specify all EventBridge API actions, use the `*` wildcard as follows\.

```
"Action": "events:*"
```

The following table lists the EventBridge API operations and corresponding actions that you can specify in an IAM policy\.


| EventBridge API operation | Required permissions | Description | 
| --- | --- | --- | 
|  [DeleteRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DeleteRule.html)  |  `events:DeleteRule`  |  Required to delete a rule\.  | 
|  [DescribeEventBus](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeEventBus.html)  |  `events:DescribeEventBus`  |  Required to list accounts that are allowed to write events to the current account's event bus\.  | 
|  [DescribeRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DescribeRule.html)  |  `events:DescribeRule`  |  Required to list the details about a rule\.  | 
|  [DisableRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_DisableRule.html)  |  `events:DisableRule`  |  Required to disable a rule\.  | 
|  [EnableRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_EnableRule.html)  |  `events:EnableRule`  |  Required to enable a rule\.  | 
|  [ListRuleNamesByTarget](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_ListRuleNamesByTarget.html)  |  `events:ListRuleNamesByTarget`  |  Required to list rules associated with a target\.  | 
|  [ListRules](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_ListRules.html)  |  `events:ListRules`  |  Required to list all rules in your account\.  | 
|  [ListTagsForResource](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_ListTagsForResource.html)  |  `events:ListTagsForResource`  |  Required to list all tags associated with an EventBridge resource\. Currently, only rules can be tagged\.  | 
|  [ListTargetsByRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_ListTargetsByRule.html)  |  `events:ListTargetsByRule`  |  Required to list all targets associated with a rule\.  | 
|  [PutEvents](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEvents.html)  |  `events:PutEvents`  |  Required to add custom events that can be matched to rules\.  | 
|  [PutPermission](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutPermission.html)  |  `events:PutPermission`  |  Required to give another account permission to write events to this account’s default event bus\.  | 
|  [PutRule](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutRule.html)  |  `events:PutRule`  |  Required to create or update a rule\.  | 
|  [PutTargets](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutTargets.html)  |  `events:PutTargets`  |  Required to add targets to a rule\.  | 
|  [RemovePermission](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RemovePermission.html)  |  `events:RemovePermission`  |  Required to revoke another account’s permissions for writing events to this account’s default event bus\.  | 
|  [RemoveTargets](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_RemoveTargets.html)  |  `events:RemoveTargets`  |  Required to remove a target from a rule\.  | 
|  [TestEventPattern](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_TestEventPattern.html)  |  `events:TestEventPattern`  |  Required to test an event pattern against a given event\.  | 