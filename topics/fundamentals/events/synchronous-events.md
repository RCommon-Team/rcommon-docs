---
description: Local/In Memory Events and Event Handling in RCommon.
---

# Synchronous Events

Local events are events which are raised and then handled in memory. Keep in mind that the term "local event" is not a technical term, but rather a way of delineating between events which are raised and handled in memory vs. events which may be raised in memory but distributed to applications outside of your domain where they are consumed/handled.&#x20;

While events are not a new thing for developers, RCommon has implemented them in a variety of meaningful patterns which allow you to jump past having to think through plumbing/infrastructure code and go right into building your application.&#x20;

### Transactional (a.k.a. Deferred) Events

As is common with business entities (domain objects in DDD terms), you will need to rely on using methods to change properties of your entities so that you can raise events (domain events in DDD terms) which notify other areas of your application that an entity has been added, updated, deleted, etc. This is all part of avoiding anemic entity models. However, in many cases you will not want to raise those events until some transactional behavior has been exercised. Common behaviors that trigger events to be raised are persistence, or other application state changes. Maintaining an in-memory repository of events ensures that your events only get published/dispatched when you explicitly instruct it your application to do so.&#x20;

The easiest way to leverage this functionality is to have your entity classes inherit from [BusinessEntity](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.BusinessEntities/BusinessEntity.cs).&#x20;

```csharp
public class Alarm : BusinessEntity<Guid>
{

    public Alarm()
    {

    }

    public string Name { get; set; }
    public bool Enabled { get; set; }

    private void EnableAlarm()
    {
        this.Enabled = true;
        this.AddLocalEvent(new AlarmEnabledLocalEvent(this));
    }

    private void DisableAlarm()
    {
        this.Enabled = false;
        this.AddLocalEvent(new AlarmDisabledLocalEvent(this));
    }
}
```

From there, you can rely on a [simple extension method](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.BusinessEntities/BusinessEntityCollectionExtensions.cs) to traverse your entity (and all objects in your graph) and publish/dispatch the local events you've stored in your business entities.&#x20;

### Entity Persistence Events

One of the biggest advantages of using the repository pattern within RCommon is that we've already implemented all the plumbing for capturing transactional events as part of the persistence workflow. Any transactional events you raise as part of your entity logic will get published/dispatched along with the entity's state changes (entity is added, changed, or deleted) just prior to the persistence occurring. This behavior allows any event handlers that your application fires to process any logic (and particularly persistence logic) as part of a single Unit of Work.&#x20;

#### Events Published/Dispatched During Persistence

| [EntityCreatedEvent](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.BusinessEntities/EntityCreatedEvent.cs) | [EntityUpdatedEvent](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.BusinessEntities/EntityUpdatedEvent.cs) | [EntityDeletedEvent](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.BusinessEntities/EntityDeletedEvent.cs) |
| ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
