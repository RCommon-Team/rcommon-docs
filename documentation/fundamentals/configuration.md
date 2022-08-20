---
description: How to configure RCommon.
---

# Configuration

The simplest way to configure RCommon is to use the configuration entry point serves as a fluent dependency injection interface for bootstrapping the framework and all its necessary dependencies. From there you'll need to choose which dependency injection container to use and instantiate the [correlated adapter](dependency-injection/di-providers/) so that RCommon knows which container to use to register and resolve dependencies from.&#x20;

### Method Extensions

There is no configuration to use our method extensions right out of gate. There is also a useful list of helper classes that may be able to help you with smaller projects.&#x20;

### Utilities

At minimum, you will need to configure a container adapter and state storage. We recommend using the "DefaultStateStorageConfiguration" as that will bootstrap both web and non-web applications with a helpful list of utility classes and simple [application state storage options](application-state.md).&#x20;

```csharp
protected void InitializeRCommon(IServiceCollection services)
{
    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(services))
        .WithStateStorage<DefaultStateStorageConfiguration>();
}
```

### [Guid Generation](../infrastructure/guid-generation.md)

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
        .WithGuidGenerator&#x3C;SequentialGuidGenerator>(x => 
            x.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString));
}</code></pre>

### [Time and Date](../infrastructure/time-and-date.md)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
        .WithDateTimeSystem<SystemTime>(x => x.Kind = DateTimeKind.Utc));
}
```

### [MediaR Pipeline](design-patterns/mediator.md)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
        .WithStateStorage<DefaultStateStorageConfiguration>()
        .WithPersistence<EFCoreConfiguration>(x =>
            x.UsingDbContext<LeaveManagementDbContext>())
        .And<DataServicesConfiguration>(x =>
            x.WithUnitOfWork<DefaultUnitOfWorkConfiguration>())
        .AddUnitOfWorkToMediatorPipeline());
}
```

### [Email Sending](../infrastructure/email-sending.md)



### [Persistence w/ Unit of Work](persistence/)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
        .WithStateStorage<DefaultStateStorageConfiguration>()
        .WithPersistence<EFCoreConfiguration>(x =>
            x.UsingDbContext<LeaveManagementDbContext>())
        .And<DataServicesConfiguration>(x =>
            x.WithUnitOfWork<DefaultUnitOfWorkConfiguration>()));
}
```

### [Exception Handling](exception-handling/)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(services))
        .And<EhabExceptionHandlingConfiguration>(x=>
            x.UsingDefaultExceptionPolicies());
}
```

