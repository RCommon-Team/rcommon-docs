---
description: How to configure RCommon.
---

# Configuration

The simplest way to configure RCommon is to use the configuration entry point serves as a fluent dependency injection interface for bootstrapping the framework and all its necessary dependencies. From there you'll need to choose which dependency injection container to use and instantiate the [correlated adapter](broken-reference) so that RCommon knows which container to use to register and resolve dependencies from.&#x20;

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

### [Guid Generation](guid-generation.md)

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithGuidGenerator&#x3C;SequentialGuidGenerator>(x => 
            x.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString));
}
</code></pre>

### [Time and Date](time-and-date.md)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .WithDateTimeSystem<SystemTime>(x => x.Kind = DateTimeKind.Utc));
}
```

### [MediaR Pipeline](../infrastructure/mediator/)

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

### [Email Sending](emailing/)



### [Persistence w/ Unit of Work](../infrastructure/persistence/)

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

### [Exception Handling](broken-reference)

```csharp
protected void InitializeRCommon(IServiceCollection services)
{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(services))
        .And<EhabExceptionHandlingConfiguration>(x=>
            x.UsingDefaultExceptionPolicies());
}
```

