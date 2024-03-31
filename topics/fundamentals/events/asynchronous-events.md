---
description: Distributed Event Handling in RCommon.
---

# Asynchronous Events

Distributed (a.k.a. Integrated) events, in contrast to [local events](synchronous-events.md), are events which are raised and published with the express intent of having them distributed to and consumed by other applications that exist outside the in-memory process which raises them. The conduit by which these events are transported is called an [Event Bus](../../patterns/message-bus/).&#x20;

RCommon currently relies solely on [MassTransit ](../../patterns/message-bus/masstransit.md)to distribute events along with the help of a simple distributed event broker which acts as a repository for any distributed events which are raised during the course of the application's runtime operations. The distributed event broker gives transactional control over when distributed events are published/dispatched. &#x20;

Distributed events can easily be added to the DistributedEventBroker like this:

```csharp
public class StudenStatusChangedDomainEventHandler : ILocalEventHandler<StudentStatusChangedDomainEvent>
{
    private readonly IDistributedEventBroker _distributedEventBroker;

    public StudentStatusChangedDomainEventHandler(IDistributedEventBroker distributedEventBroker)
    {
        this._distributedEventBroker = distributedEventBroker;
    }

    public Task Handle(LeadStatusChangedDomainEvent notification, CancellationToken cancellationToken)
    {
        this._distributedEventBroker.AddDistributedEvent(
            new StudentStatusChangedEvent(notification.Status, notification.StudentId));
        return Task.CompletedTask;
    }
}
```

...and then can easily be published like this:&#x20;

```csharp
public async Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken, RequestHandlerDelegate<TResponse> next)
{
    var response = default(TResponse);
    var typeName = request.GetGenericTypeName();

    try
    {
        using (var unitOfWork = this._unitOfWorkScopeFactory.Create(TransactionMode.Default))
        {
            _logger.LogInformation("----- Begin transaction {UnitOfWorkTransactionId} for {CommandName} ({@Command})", 
                this._unitOfWorkManager.CurrentUnitOfWork.TransactionId, typeName, request);

            response = await next();

            _logger.LogInformation("----- Commit transaction {UnitOfWorkTransactionId} for {CommandName}", 
                this._unitOfWorkManager.CurrentUnitOfWork.TransactionId, typeName);

            unitOfWork.Commit();
        }

        //Perform MassTransit publish events
        await _distributedEventBroker.PublishDistributedEvents(cancellationToken);


        return response;
    }
    catch (ApplicationException ex)
    {
        _logger.LogError(ex, "ERROR Handling transaction for {CommandName} ({@Command})", typeName, request);

        throw;
    }
}
```

As seen above, the DistributedUnitOfWorkBehavior ties together the Unit of Work pattern with the DistributedEventBroker's publishing behavior to create a means of handling transactions and then distributing events as part of a transactional workflow. &#x20;
