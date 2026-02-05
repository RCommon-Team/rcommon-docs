---
description: >-
  A working example of the CQRS pattern using RCommon's command bus, query bus,
  FluentValidation, and in-memory caching.
---

# CQRS

### Overview

This example demonstrates how to use RCommon's built-in CQRS (Command Query Responsibility Segregation) implementation. It showcases command and query buses, command handlers, query handlers, FluentValidation integration, and in-memory caching of dynamically compiled expressions.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/ApplicationServices/Examples.ApplicationServices.CQRS).
{% endhint %}

### Configuration

```csharp
using Examples.ApplicationServices.CQRS;
using Examples.ApplicationServices.CQRS.Validators;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.ApplicationServices;
using RCommon.Caching;
using RCommon.FluentValidation;
using RCommon.MemoryCache;
using System.Diagnostics;
using System.Reflection;

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
                        .WithCQRS<CqrsBuilder>(cqrs =>
                        {
                            // You can do it this way which is pretty straight forward but verbose
                            //cqrs.AddQueryHandler<TestQueryHandler, TestQuery, TestDto>();
                            //cqrs.AddCommandHandler<TestCommandHandler, TestCommand, IExecutionResult>();

                            // Or this way which uses a little magic but is simple
                            cqrs.AddCommandHandlers((typeof(Program).GetTypeInfo().Assembly));
                            cqrs.AddQueryHandlers((typeof(Program).GetTypeInfo().Assembly));
                        })
                        .WithValidation<FluentValidationBuilder>(validation =>
                        {
                            validation.AddValidatorsFromAssemblyContaining(typeof(TestCommand));

                            validation.UseWithCqrs(options =>
                            {
                                options.ValidateCommands = true;
                                options.ValidateQueries = true;
                            });
                        })
                        .WithMemoryCaching<InMemoryCachingBuilder>(cache =>
                        {
                            cache.Configure(x =>
                            {
                                x.ExpirationScanFrequency = TimeSpan.FromMinutes(1);
                            });
                            cache.CacheDynamicallyCompiledExpressions();
                        });


                    services.AddTransient<ITestApplicationService, TestApplicationService>();

                }).Build();

    Console.WriteLine("Example Starting");
    var appService = host.Services.GetRequiredService<ITestApplicationService>();
    var commandResult = await appService.ExecuteTestCommand(new TestCommand("test"));
    var queryResult = await appService.ExecuteTestQuery(new TestQuery());

    Console.WriteLine(commandResult.ToString());
    Console.WriteLine(queryResult.Message);

    Console.WriteLine("Example Complete");
    Console.ReadLine();
}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());

}
```

### Implementation

#### Command

The command implements `ICommand<IExecutionResult>` which tells the command bus what type of result to expect.

```csharp
using RCommon.Models.Commands;
using RCommon.Models.ExecutionResults;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS
{
    public class TestCommand : ICommand<IExecutionResult>
    {
        public TestCommand(string message)
        {
            Message = message;
        }

        public string Message { get; }
    }
}
```

#### Command Handler

The command handler implements `ICommandHandler<IExecutionResult, TestCommand>` which maps a handler to a specific command.

```csharp
using RCommon;
using RCommon.ApplicationServices.Commands;
using RCommon.Models.ExecutionResults;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS
{
    public class TestCommandHandler : ICommandHandler<IExecutionResult, TestCommand>
    {
        public async Task<IExecutionResult> HandleAsync(TestCommand command, CancellationToken cancellationToken)
        {
            Console.WriteLine("{0} successfully handled {1}", new object[] { this.GetGenericTypeName(), command });
            return await Task.FromResult(new SuccessExecutionResult());
        }
    }
}
```

#### Command Validator

FluentValidation is integrated into the CQRS pipeline to automatically validate commands before they reach handlers.

```csharp
using FluentValidation;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS.Validators
{
    public class TestCommandValidator : AbstractValidator<TestCommand>
    {
        public TestCommandValidator()
        {
            RuleFor(command => command.Message).NotNull().NotEmpty();
        }
    }
}
```

#### Query

The query implements `IQuery<TestDto>` which defines the expected return type.

```csharp
using RCommon.Models.Queries;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS
{
    public class TestQuery : IQuery<TestDto>
    {
        public TestQuery()
        {

        }
    }
}
```

#### Query Handler

```csharp
using RCommon;
using RCommon.ApplicationServices.Queries;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS
{
    public class TestQueryHandler : IQueryHandler<TestQuery, TestDto>
    {
        private readonly IQueryBus _queryBus;

        public TestQueryHandler(IQueryBus queryBus)
        {
            _queryBus = queryBus;
        }
        public async Task<TestDto> HandleAsync(TestQuery query, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("{0} successfully handled {1}", new object[] { this.GetGenericTypeName(), query });
            return await Task.FromResult(new TestDto("Success!"));

        }
    }
}
```

#### Application Service

The application service ties together the command bus and query bus, dispatching commands and queries through the CQRS pipeline.

```csharp
using RCommon.ApplicationServices.Commands;
using RCommon.ApplicationServices.Queries;
using RCommon.Models.ExecutionResults;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.ApplicationServices.CQRS
{
    public class TestApplicationService : ITestApplicationService
    {
        private readonly ICommandBus _commandBus;
        private readonly IQueryBus _queryBus;

        public TestApplicationService(ICommandBus commandBus, IQueryBus queryBus)
        {
            _commandBus = commandBus;
            _queryBus = queryBus;
        }

        public async Task<TestDto> ExecuteTestQuery(TestQuery query)
        {
            return await _queryBus.DispatchQueryAsync(query, CancellationToken.None);
        }

        public async Task<IExecutionResult> ExecuteTestCommand(TestCommand command)
        {
            return await _commandBus.DispatchCommandAsync(command, CancellationToken.None);
        }
    }
}
```

### Learn More

{% content-ref url="../topics/patterns/cqrs/commands.md" %}
[commands.md](../topics/patterns/cqrs/commands.md)
{% endcontent-ref %}

{% content-ref url="../topics/patterns/cqrs/queries.md" %}
[queries.md](../topics/patterns/cqrs/queries.md)
{% endcontent-ref %}
