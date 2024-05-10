---
description: How to configure RCommon.
---

# Configuration

The simplest way to configure RCommon is to use the configuration entry point which serves as a fluent dependency injection interface for bootstrapping the libraries and all its necessary dependencies.&#x20;

### Method Extensions

There is no configuration to use our method extensions right out of gate. There is also a useful list of helper classes that may be able to help you with smaller projects.&#x20;

### Utilities

At minimum, you will need to the dependency injection container. This will bootstrap your application with RCommon.. The minimum configuration provides you will access to many utility classes and services.

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

### [Persistence w/ Unit of Work](../patterns/persistence/)

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

### [Event Handling](events/)

```csharp
services.AddRCommon()
    .WithEventHandling<InMemoryEventBusBuilder>(eventHandling =>
    {
        eventHandling.AddProducer<PublishWithEventBusEventProducer>();
        eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
    });
    
// OR - the MediatR version (or both)
services.AddRCommon()
    .WithEventHandling<MediatREventHandlingBuilder>(eventHandling =>
    {
        eventHandling.AddProducer<PublishWithMediatREventProducer>();
        
        eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
    })
    // OR all of them
    .WithEventHandling<MassTransitEventHandlingBuilder>(eventHandling =>
    {
        eventHandling.UsingInMemory((context, cfg) =>
        {
            cfg.ConfigureEndpoints(context);
        });
    
        eventHandling.AddProducer<PublishWithMassTransitEventProducer>();
        eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
    });

```

### [Mediator](../patterns/mediator/)

```csharp
builder.Services.AddRCommon()
    .WithMediator<MediatRBuilder>(mediator =>
    {
        mediator.AddRequest<CreateLeaveAllocationCommand, BaseCommandResponse, CreateLeaveAllocationCommandHandler>();
        mediator.AddRequest<DeleteLeaveAllocationCommand, DeleteLeaveAllocationCommandHandler>();
        mediator.AddRequest<UpdateLeaveAllocationCommand, UpdateLeaveAllocationCommandHandler>();
        mediator.AddRequest<GetLeaveAllocationDetailRequest, LeaveAllocationDto, GetLeaveAllocationDetailRequestHandler>();
        mediator.AddRequest<GetLeaveAllocationListRequest, List<LeaveAllocationDto>, GetLeaveAllocationListRequestHandler>();
        mediator.AddRequest<CreateLeaveAllocationCommand, BaseCommandResponse, CreateLeaveAllocationCommandHandler>();
        mediator.AddRequest<DeleteLeaveAllocationCommand, DeleteLeaveAllocationCommandHandler>();
        mediator.AddRequest<UpdateLeaveAllocationCommand, UpdateLeaveAllocationCommandHandler>();
        mediator.AddRequest<CreateLeaveRequestCommand, BaseCommandResponse, CreateLeaveRequestCommandHandler>();
        mediator.AddRequest<DeleteLeaveRequestCommand, DeleteLeaveRequestCommandHandler>();
        mediator.AddRequest<UpdateLeaveRequestCommand, UpdateLeaveRequestCommandHandler>();
        mediator.AddRequest<CreateLeaveTypeCommand, BaseCommandResponse, CreateLeaveTypeCommandHandler>();
        mediator.AddRequest<GetLeaveRequestDetailRequest, LeaveRequestDto, GetLeaveRequestDetailRequestHandler>();
        mediator.AddRequest<GetLeaveRequestListRequest, List<LeaveRequestListDto>, GetLeaveRequestListRequestHandler>();
        mediator.AddRequest<CreateLeaveTypeCommand, BaseCommandResponse, CreateLeaveTypeCommandHandler>();
        mediator.AddRequest<DeleteLeaveTypeCommand, DeleteLeaveTypeCommandHandler>();
        mediator.AddRequest<UpdateLeaveTypeCommand, UpdateLeaveTypeCommandHandler>();
        mediator.AddRequest<GetLeaveTypeDetailRequest, LeaveTypeDto, GetLeaveTypeDetailRequestHandler>();
        mediator.AddRequest<GetLeaveTypeListRequest, List<LeaveTypeDto>, GetLeaveTypeListRequestHandler>();

        mediator.Configure(config =>
        {
            config.RegisterServicesFromAssemblies((typeof(ApplicationServicesRegistration).GetTypeInfo().Assembly));
        });
        mediator.AddLoggingToRequestPipeline();
        mediator.AddUnitOfWorkToRequestPipeline();
    })
```

### [Validation](validation/)

```csharp
var host = Host.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((context, builder) =>
            {

                ConfigurationContainer.Configuration = builder
                    .Build();
            })
            .ConfigureServices(services =>
            {
                // Configure RCommon
                services.AddRCommon()
                    .WithValidation<FluentValidationBuilder>(validation =>
                    {
                        validation.AddValidatorsFromAssemblyContaining(typeof(TestDto));
                    });
                
                services.AddTransient<ITestApplicationService, TestApplicationService>();

            }).Build();
```

### [CQRS](../patterns/cqrs/)

```csharp
// Configure RCommon
services.AddRCommon()
    .WithCQRS<CqrsBuilder>(cqrs =>
    {
        cqrs.AddQueryHandler<TestQueryHandler, TestQuery, TestDto>();
        cqrs.AddCommandHandler<TestCommandHandler, TestCommand, IExecutionResult>();
    })
    .WithValidation<FluentValidationBuilder>(validation =>
    {
        validation.AddValidatorsFromAssemblyContaining(typeof(TestCommand));

        validation.UseWithCqrs(options =>
        {
            options.ValidateCommands = true;
            options.ValidateQueries = true;
        });
    });

services.AddTransient<ITestApplicationService, TestApplicationService>();
                    
```
