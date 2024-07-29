---
description: MediatR implementation in RCommon.
---

# MediatR

[MediatR ](https://github.com/jbogard/MediatR)is one of the most popular Mediator pattern implementations in the .NET community. It is well documented and well used.&#x20;

### Usage

```csharp
builder.Services.AddRCommon()
    .WithMediator<MediatRBuilder>(mediator =>
    {
        // Registering abstracted mediator requests
        mediator.AddRequest<CreateLeaveAllocationCommand, BaseCommandResponse, CreateLeaveAllocationCommandHandler>();
        mediator.AddRequest<DeleteLeaveAllocationCommand, DeleteLeaveAllocationCommandHandler>();
        mediator.AddRequest<UpdateLeaveAllocationCommand, UpdateLeaveAllocationCommandHandler>();
        mediator.AddRequest<GetLeaveAllocationDetailRequest, LeaveAllocationDto, GetLeaveAllocationDetailRequestHandler>();
        mediator.AddRequest<GetLeaveAllocationListRequest, List<LeaveAllocationDto>, GetLeaveAllocationListRequestHandler>();
        
        // Conventional dependency injection w/ MediatR
        mediator.Configure(config =>
        {
            config.RegisterServicesFromAssemblies((typeof(ApplicationServicesRegistration).GetTypeInfo().Assembly));
        });
        
        // Utilizing MediatR pipeline registrations
        mediator.AddLoggingToRequestPipeline();
        mediator.AddUnitOfWorkToRequestPipeline();
    });
```



### Common MediatR Pipeline Behaviors



{% content-ref url="validator-behavior.md" %}
[validator-behavior.md](validator-behavior.md)
{% endcontent-ref %}

{% content-ref url="unit-of-work-behavior.md" %}
[unit-of-work-behavior.md](unit-of-work-behavior.md)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="logging-behavior.md" %}
[logging-behavior.md](logging-behavior.md)
{% endcontent-ref %}
