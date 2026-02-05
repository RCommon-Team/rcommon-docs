---
description: >-
  A working example of standalone FluentValidation integration using RCommon's
  validation service with assembly scanning for validators.
---

# Validation: Fluent Validation

### Overview

This example demonstrates how to use RCommon's FluentValidation integration as a standalone validation service. It shows how to configure assembly scanning for validators and how to use the `IValidationService` to validate DTOs, returning a `ValidationOutcome` with results.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/Validation/Examples.Validation.FluentValidation).
{% endhint %}

### Configuration

```csharp
using Examples.Validation.FluentValidation;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.ApplicationServices;
using RCommon.FluentValidation;
using System.Diagnostics;

try
{
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

    Console.WriteLine("Example Starting");
    var appService = host.Services.GetRequiredService<ITestApplicationService>();
    var validationOutcome1 = await appService.ExecuteTestMethod(new TestDto("")); // Will fail
    var validationOutcome2 = await appService.ExecuteTestMethod(new TestDto("test")); // Will pass

    Console.WriteLine(validationOutcome1.ToString());
    Console.WriteLine("Validation Outcome 1 complete...");
    Console.WriteLine(validationOutcome2.ToString());
    Console.WriteLine("Validation Outcome 2 complete...");

    Console.WriteLine("Example Complete");
    Console.ReadLine();
}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());

}
```

### Implementation

#### DTO

A simple record with a `Message` property to validate.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Validation.FluentValidation
{
    public record TestDto
    {
        public TestDto(string message)
        {
            Message = message;
        }

        public string Message { get; }
    }
}
```

#### Validator

The validator uses FluentValidation's `AbstractValidator<T>` to define validation rules. Validators are automatically discovered through assembly scanning.

```csharp
using FluentValidation;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Validation.FluentValidation.Validators
{
    public class TestDtoValidator : AbstractValidator<TestDto>
    {
        public TestDtoValidator()
        {
            RuleFor(command => command.Message).NotNull().NotEmpty();
        }
    }
}
```

#### Application Service

The application service uses `IValidationService` to validate DTOs and return a `ValidationOutcome`.

```csharp
using RCommon.ApplicationServices.Commands;
using RCommon.ApplicationServices.Queries;
using RCommon.ApplicationServices.Validation;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Validation.FluentValidation
{
    public class TestApplicationService : ITestApplicationService
    {
        private readonly IValidationService _validationService;

        public TestApplicationService(IValidationService validationService)
        {
            _validationService = validationService;
        }

        public async Task<ValidationOutcome> ExecuteTestMethod(TestDto dto)
        {
            return await _validationService.ValidateAsync(dto);
        }
    }
}
```

### Learn More

{% content-ref url="../topics/fundamentals/validation/fluent-validation.md" %}
[fluent-validation.md](../topics/fundamentals/validation/fluent-validation.md)
{% endcontent-ref %}
