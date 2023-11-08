---
description: How to configure RCommon.
---

# Configuration

The simplest way to configure RCommon is to use the configuration entry point serves as a fluent dependency injection interface for bootstrapping the framework and all its necessary dependencies. From there you'll need to choose which dependency injection container to use and instantiate the [correlated adapter](../../../v1/fundamentals/dependency-injection/di-providers/) so that RCommon knows which container to use to register and resolve dependencies from.&#x20;

### Method Extensions

There is no configuration to use our method extensions right out of gate. There is also a useful list of helper classes that may be able to help you with smaller projects.&#x20;

### Utilities

At minimum, you will need to configure a container adapter. This will bootstrap your application with RCommon using the dependency injection container of your choice. The minimum configuration provides you will access to many utility classes and services.&#x20;

```csharp
protected void InitializeRCommon(IServiceCollection services)
{
    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(services));
}
```

### [Guid Generation](../../../v1/infrastructure/guid-generation.md)

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithGuidGenerator&#x3C;SequentialGuidGenerator>(x => 
            x.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString));
}
</code></pre>

### [Time and Date](../../../v1/infrastructure/time-and-date.md)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithDateTimeSystem<SystemTime>(x => x.Kind = DateTimeKind.Utc));
}
```

### [MediaR Pipeline](../../../v1/fundamentals/design-patterns/mediator.md)

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithStateStorage&#x3C;DefaultStateStorageConfiguration>()
        .WithPersistence&#x3C;EFCoreConfiguration>(x =>
            x.UsingDbContext&#x3C;LeaveManagementDbContext>())
        .And&#x3C;DataServicesConfiguration>(x =>
            x.WithUnitOfWork&#x3C;DefaultUnitOfWorkConfiguration>())
        .AddUnitOfWorkToMediatorPipeline());
}
</code></pre>

### [Email Sending](../../../v1/infrastructure/emailing/)



### [Persistence w/ Unit of Work](../../../v1/fundamentals/persistence/)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithStateStorage<DefaultStateStorageConfiguration>()
        .WithPersistence<EFCoreConfiguration>(x =>
            x.UsingDbContext<LeaveManagementDbContext>())
        .And<DataServicesConfiguration>(x =>
            x.WithUnitOfWork<DefaultUnitOfWorkConfiguration>()));
}
```

### [Exception Handling](../../../v1/fundamentals/exception-handling/)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(services))
        .And<EhabExceptionHandlingConfiguration>(x=>
            x.UsingDefaultExceptionPolicies());
}
```

