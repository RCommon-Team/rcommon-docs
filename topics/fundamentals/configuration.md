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
// Add RCommon services
builder.Services.AddRCommon();
```

### [Guid Generation](../infrastructure/guid-generation.md)

```csharp
// Add RCommon services
builder.Services.AddRCommon()
    .WithSequentialGuidGenerator(guid => guid.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString);
```

### [Time and Date](../infrastructure/time-and-date.md)&#x20;

```csharp
// Add RCommon services
builder.Services.AddRCommon()
    .WithDateTimeSystem(dateTime => dateTime.Kind = DateTimeKind.Utc);
```

### [Email Sending](../infrastructure/emailing/)

```csharp
// Add RCommon services
builder.Services.AddRCommon()
    .WithSendGridEmailServices(x =>
    {
        var sendGridSettings = builder.Configuration.Get<SendGridEmailSettings>();
        x.SendGridApiKey = sendGridSettings.SendGridApiKey;
        x.FromNameDefault = sendGridSettings.FromNameDefault;
        x.FromEmailDefault = sendGridSettings.FromEmailDefault;
    });
```

### [Persistence w/ Unit of Work](persistence/)

```csharp
// Add RCommon services
builder.Services.AddRCommon()
    .WithPersistence<EFCorePerisistenceBuilder, DefaultUnitOfWorkBuilder>(ef => // Repository/ORM configuration. We could easily swap out to NHibernate without impact to domain service up through the stack
    {
        // Add all the DbContexts here
        ef.AddDbContext<LeaveManagementDbContext>("LeaveManagementConnectionString", options =>
        {
            options.UseSqlServer(
                builder.Configuration.GetConnectionString("LeaveManagementConnectionString"));
        });
        ef.SetDefaultDataStore(dataStore =>
        {
            dataStore.DefaultDataStoreName = "LeaveManagementConnectionString";
        });
    }, unitOfWork =>
    {
        unitOfWork.SetOptions(options =>
        {
            options.AutoCompleteScope = true;
            options.DefaultIsolation = IsolationLevel.ReadCommitted;
        });
    });
```

### [Event Handling](broken-reference)

```csharp
// Configure RCommon
services.AddRCommon()
    .WithEventHandling<InMemoryEventBusBuilder>(eventHandling =>
    {
        eventHandling.AddProducer<PublishWithEventBusEventProducer>();
        eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
    });
```

