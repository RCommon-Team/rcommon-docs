---
description: Unit of Work behavior in MediatR pipeline within RCommon.
---

# Unit of Work Behavior

The [unit of work behavior](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.ApplicationServices/Behaviors/UnitOfWorkBehavior.cs) is useful any time you want to de-couple your transaction handling from your application logic. It can be added by doing this in your dependency injection section. The Unit of Work behavior is really only useful when coupled with [Persistence](../../fundamentals/persistence/). &#x20;

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithStateStorage<DefaultStateStorageConfiguration>()
        .WithPersistence<EFCoreConfiguration>(x =>
            x.UsingDbContext<LeaveManagementDbContext>())
        .And<DataServicesConfiguration>(x =>
            x.WithUnitOfWork<DefaultUnitOfWorkConfiguration>())
        .AddUnitOfWorkToMediatorPipeline());
}
```
