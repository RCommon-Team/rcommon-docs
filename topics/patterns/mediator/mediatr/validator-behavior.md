---
description: Validator Behavior in MediatR pipeline within RCommon.
---

# Validator Behavior

The validator behavior is useful anytime you want to de-couple request/DTO validation from your application logic. This is implemented in in MediatR via a [FluentValidation](https://github.com/jbogard/MediatR?tab=readme-ov-file) extension.&#x20;

### Implementation

The validator behavior is injected as part of the MediatR pipeline and performs validations on incoming requests which have objects that match the validation pattern  that is registered automatically via dependency injection (see usage):

```csharp
public class ValidatorBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : class, IAppRequest<TResponse>
{
    private readonly IValidationService _validationService;
    private readonly ILogger<ValidatorBehavior<TRequest, TResponse>> _logger;

    public ValidatorBehavior(IValidationService validationService, ILogger<ValidatorBehavior<TRequest, TResponse>> logger)
    {
        _validationService = validationService;
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }

    public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken cancellationToken)
    {
        var typeName = request.GetGenericTypeName();

        _logger.LogInformation("----- Validating command {CommandType}", typeName);
        await _validationService.ValidateAsync<TRequest>(request, true, cancellationToken);
        return await next();
    }
}
```

### Usage

Begin with dependency injection:

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
        mediator.AddValidationToRequestPipeline();
    });
```



Now create your validators using [FluentValidation ](../../../fundamentals/validation/fluent-validation.md)[which is very well documented](https://docs.fluentvalidation.net/en/latest/).&#x20;
