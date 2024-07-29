---
description: Unit of Work behavior in MediatR pipeline within RCommon.
---

# Unit of Work Behavior

The [unit of work behavior](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.ApplicationServices/Behaviors/UnitOfWorkBehavior.cs) is useful any time you want to de-couple your transaction handling from your application logic. It can be added by doing this in your dependency injection section. The Unit of Work behavior is really only useful when coupled with [Persistence](../../persistence/) but should be used with the knowledge that all of your requests and the operations within will be wrapped with a transaction which could add some overhead to your application. That said, many enterprise applications have multi-step operations in most of their requests so this may be a good behavior to use.&#x20;

### Implementation

```csharp
public class UnitOfWorkRequestBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest
{
    private readonly ILogger<UnitOfWorkRequestBehavior<TRequest, TResponse>> _logger;
    private readonly IUnitOfWorkFactory _unitOfWorkScopeFactory;

    public UnitOfWorkRequestBehavior(IUnitOfWorkFactory unitOfWorkScopeFactory,
        ILogger<UnitOfWorkRequestBehavior<TRequest, TResponse>> logger)
    {
        _unitOfWorkScopeFactory = unitOfWorkScopeFactory ?? throw new ArgumentException(nameof(IUnitOfWorkFactory));
        _logger = logger ?? throw new ArgumentException(nameof(ILogger));
    }

    public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken cancellationToken)
    {
        var response = default(TResponse);
        var typeName = request.GetGenericTypeName();

        try
        {
            using (var unitOfWork = this._unitOfWorkScopeFactory.Create(TransactionMode.Default))
            {
                _logger.LogInformation("----- Begin transaction {UnitOfWorkTransactionId} for {CommandName} ({@Command})",
                    unitOfWork.TransactionId, typeName, request);

                response = await next(); // Let everything else in the request happen

                _logger.LogInformation("----- Commit transaction {UnitOfWorkTransactionId} for {CommandName}",
                    unitOfWork.TransactionId, typeName);

                unitOfWork.Commit();
            }

            return response;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "ERROR Handling transaction for {CommandName} ({@Command})", typeName, request);

            throw;
        }
    }
}
```

### Usage

```csharp
builder.Services.AddRCommon()
    .WithMediator<MediatRBuilder>(mediator =>
    {
        // Conventional dependency injection w/ MediatR
        mediator.Configure(config =>
        {
            config.RegisterServicesFromAssemblies((typeof(ApplicationServicesRegistration).GetTypeInfo().Assembly));
        });
        
        // Utilizing MediatR pipeline registrations
        mediator.AddUnitOfWorkToRequestPipeline();
    });
```
