---
description: Unit of work pattern used within RCommon.
---

# Unit of Work

You can use the unit of work pattern in your code by using the UnitOfWorkScope interface acquired by using the UnitOfWorkScopeFactory.&#x20;

```csharp
var customer = new Customer { FirstName = "John", LastName = "Doe" };
var salesPerson = new SalesPerson { FirstName = "Jane", LastName = "Doe", SalesQuota = 2000 };

// Setup required services
var scopeFactory = this.ServiceProvider.GetService<IUnitOfWorkScopeFactory>();

using (var scope = scopeFactory.Create(TransactionMode.Default))
{
    var customerRepo = this.ServiceProvider.GetService<IFullFeaturedRepository<Customer>>();
    repo.DataStoreName = "TestDbContext";
    await customerRepo.AddAsync(customer);
    var salesPersonRepo = this.ServiceProvider.GetService<IFullFeaturedRepository<SalesPerson>>();
    salesPersonRepo.DataStoreName = "TestDbContext";
    await salesPersonRepo.AddAsync(salesPerson);
    scope.Commit();
}
```

Any repository implemented in RCommon implicitly uses the Unit of Work pattern in a loosely coupled manner. The UnitOfWorkScope class essentially wraps [TransactionScope ](https://docs.microsoft.com/en-us/dotnet/api/system.transactions.transactionscope?view=net-6.0)so all the rules of using TransactionScope still apply.&#x20;

### Unit of Work in MediatR Behavior Pipeline

In addition to being able to use the UnitOfWorkScope procedurally in your applications, you can also enlist the Unit of Work has a behavior in the MediatR pipeline:

```csharp
public class UnitOfWorkBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
        where TRequest : IRequest<TResponse>
{
    private readonly ILogger<UnitOfWorkBehavior<TRequest, TResponse>> _logger;
    private readonly IUnitOfWorkScopeFactory _unitOfWorkScopeFactory;
    private readonly IUnitOfWorkManager _unitOfWorkManager;

    public UnitOfWorkBehavior(IUnitOfWorkScopeFactory unitOfWorkScopeFactory, IUnitOfWorkManager unitOfWorkManager,
        ILogger<UnitOfWorkBehavior<TRequest, TResponse>> logger)
    {
        _unitOfWorkScopeFactory = unitOfWorkScopeFactory ?? throw new ArgumentException(nameof(IUnitOfWorkScopeFactory));
        _unitOfWorkManager = unitOfWorkManager  ?? throw new ArgumentException(nameof(IUnitOfWorkManager)); 
        _logger = logger ?? throw new ArgumentException(nameof(ILogger));
    }

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

                response = await next(); // Business logic and peristence happens here

                _logger.LogInformation("----- Commit transaction {UnitOfWorkTransactionId} for {CommandName}", 
                    this._unitOfWorkManager.CurrentUnitOfWork.TransactionId, typeName);

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

...and is configured as:

```csharp
ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
    .WithStateStorage<DefaultStateStorageConfiguration>()
        x.WithUnitOfWork<DefaultUnitOfWorkConfiguration>()
    )
    .AddUnitOfWorkToMediatorPipeline() // Add Unit of Work to Mediator Pipeline
    .WithPersistence<EFCoreConfiguration>(x =>
        x.UsingDbContext<LeaveManagementDbContext>());
```
