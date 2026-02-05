---
description: >-
  A working example of the Mediator pattern using MediatR through RCommon's
  abstraction layer with notifications, requests, and request/response patterns.
---

# Mediator: MediatR

### Overview

This example demonstrates how to use the Mediator pattern through RCommon's MediatR integration. It showcases three key patterns:

* **Publish (Notifications)** - broadcast to multiple handlers
* **Send (Requests)** - dispatch to a single handler with no response
* **Send with Response** - dispatch to a single handler that returns a response

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/Mediator/Examples.Mediator.MediatR).
{% endhint %}

### Configuration

```csharp
using Examples.Mediator.MediatR;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling.Producers;
using RCommon.Mediator;
using RCommon.Mediator.MediatR;
using RCommon.MediatR;
using RCommon.MediatR.Producers;
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
                        .WithMediator<MediatRBuilder>(mediator =>
                        {
                            mediator.AddNotification<TestNotification, TestNotificationHandler>();
                            mediator.AddRequest<TestRequest, TestRequestHandler>();
                            mediator.AddRequest<TestRequestWithResponse, TestResponse, TestRequestHandlerWithResponse>();

                            // Additional configurations can be set like below
                            mediator.Configure(config =>
                            {
                                config.RegisterServicesFromAssemblies((typeof(Program).GetTypeInfo().Assembly));
                            });
                        });

                }).Build();

    Console.WriteLine("Example Starting");
    var mediatorService = host.Services.GetService<IMediatorService>();
    var notification = new TestNotification(DateTime.Now, Guid.NewGuid());
    var request = new TestRequest(DateTime.Now, Guid.NewGuid());
    var requestWithResponse = new TestRequestWithResponse(DateTime.Now, Guid.NewGuid());

    await mediatorService.Publish(notification); // For multiple handlers
    await mediatorService.Send(request); // For a single endpoint

    var response = await mediatorService.Send<TestRequestWithResponse, TestResponse>(requestWithResponse); // For a single endpoint with a response
    Console.WriteLine("Response: {0}", response.Message);

    Console.WriteLine("Example Complete");
    Console.ReadLine();
}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());

}
```

### Implementation

#### Notification (Publish)

A notification is broadcast to all registered handlers. The notification implements `IAppNotification`.

```csharp
using MediatR;
using RCommon.EventHandling;
using RCommon.Mediator;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestNotification : IAppNotification
    {
        public TestNotification(DateTime dateTime, Guid guid)
        {
            DateTime = dateTime;
            Guid = guid;
        }

        public TestNotification()
        {

        }

        public DateTime DateTime { get; }
        public Guid Guid { get; }
    }
}
```

#### Notification Handler

```csharp
using MediatR;
using RCommon;
using RCommon.EventHandling.Subscribers;
using RCommon.Mediator;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestNotificationHandler : ISubscriber<TestNotification>
    {
        public TestNotificationHandler()
        {

        }

        public async Task HandleAsync(TestNotification @event, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("{0} just handled this notification {1}", new object[] { this.GetGenericTypeName(), @event });
            await Task.CompletedTask;
        }
    }
}
```

#### Request (Send)

A request is dispatched to a single handler. The request implements `IAppRequest`.

```csharp
using MediatR;
using RCommon.EventHandling;
using RCommon.Mediator;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestRequest : IAppRequest
    {
        public TestRequest(DateTime dateTime, Guid guid)
        {
            DateTime = dateTime;
            Guid = guid;
        }

        public TestRequest()
        {

        }

        public DateTime DateTime { get; }
        public Guid Guid { get; }
    }
}
```

#### Request Handler

```csharp
using MediatR;
using Microsoft.Extensions.Logging;
using RCommon;
using RCommon.EventHandling.Subscribers;
using RCommon.Mediator;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestRequestHandler : IAppRequestHandler<TestRequest>
    {
        public TestRequestHandler()
        {

        }

        public async Task HandleAsync(TestRequest request, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("{0} just handled this request {1}", new object[] { this.GetGenericTypeName(), request });
            await Task.CompletedTask;
        }
    }
}
```

#### Request with Response (Send)

A request that returns a response. The request implements `IAppRequest<TResponse>`.

```csharp
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestRequestWithResponse : IAppRequest<TestResponse>
    {
        public TestRequestWithResponse(DateTime dateTime, Guid guid)
        {
            DateTime = dateTime;
            Guid = guid;
        }

        public DateTime DateTime { get; }
        public Guid Guid { get; }
    }
}
```

#### Request Handler with Response

```csharp
using RCommon;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestRequestHandlerWithResponse : IAppRequestHandler<TestRequestWithResponse, TestResponse>
    {
        public TestRequestHandlerWithResponse()
        {

        }

        public async Task<TestResponse> HandleAsync(TestRequestWithResponse request, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("{0} just handled this request {1}", new object[] { this.GetGenericTypeName(), request });

            var response = new TestResponse("Test Response Worked");
            return await Task.FromResult(response);
        }
    }
}
```

#### Response

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Mediator.MediatR
{
    public class TestResponse
    {
        public TestResponse(string message)
        {
            Message = message;
        }

        public string Message { get; }
    }
}
```

### Learn More

{% content-ref url="../topics/patterns/mediator/mediatr/README.md" %}
[README.md](../topics/patterns/mediator/mediatr/README.md)
{% endcontent-ref %}
