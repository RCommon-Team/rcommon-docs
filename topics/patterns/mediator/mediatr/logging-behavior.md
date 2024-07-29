---
description: Logging behavior in the MediatR pipeline within RCommon.
---

# Logging Behavior

The [logging behavior](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.ApplicationServices/Behaviors/LoggingBehavior.cs) is useful any time you want to log an incoming request in the MediatR pipeline.



### Implementation

```csharp
public class LoggingRequestBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest
{
    private readonly ILogger<LoggingRequestBehavior<TRequest, TResponse>> _logger;
    public LoggingRequestBehavior(ILogger<LoggingRequestBehavior<TRequest, TResponse>> logger) => _logger = logger;


    public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken cancellationToken)
    {
        _logger.LogInformation("----- Handling command {CommandName} ({@Command})", request.GetGenericTypeName(), request);
        var response = await next();
        _logger.LogInformation("----- Command {CommandName} handled - response: {@Response}", request.GetGenericTypeName(), response);

        return response;
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
        mediator.AddLoggingToRequestPipeline();
    });
```
