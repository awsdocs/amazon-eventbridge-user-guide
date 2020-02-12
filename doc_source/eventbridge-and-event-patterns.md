# Events and Event Patterns in EventBridge<a name="eventbridge-and-event-patterns"></a>

Events in Amazon EventBridge are represented as JSON objects\. All events have a similar structure, and the same top\-level fields\.

EventBridge rules use event patterns to match on AWS events on an event bus\. When a pattern matches, the rule routes that event to a target\.

**Topics**
+ [AWS Events](aws-events.md)
+ [Event Patterns](filtering-examples-structure.md)
+ [Matching Null Values and Empty Strings in EventBridge Event Patterns](eventbridge-event-patterns-null-and-empty-strings.md)
+ [Arrays in EventBridge Event Patterns](arrays-in-eventbridge-event-patterns.md)
+ [Content\-based Filtering with Event Patterns](content-filtering-with-event-patterns.md)