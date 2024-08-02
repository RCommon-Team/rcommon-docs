---
description: Wolverine event handling implementation in RCommon.
---

# Wolverine

### Overview

Most enterprise applications have a need for a simple in-memory event bus which implements the [observer pattern](https://en.wikipedia.org/wiki/Observer\_pattern) through the [pub/sub pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe\_pattern). RCommon implements a simple dependency-free event bus for this utility but there are some advantages to using [Wolverine](https://wolverine.netlify.app/). Wolverine is a feature rich event bus/message bus implementation that offers event durability, multi-tenancy, among other useful utilities in distributed applications.&#x20;

### Configuration

```csharp
using Examples.Messaging.Wolverine;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling;
using RCommon.EventHandling.Producers;
using RCommon.Wolverine.Producers;
using System.Diagnostics;
using Wolverine;

try
{
    var host = Host.CreateDefaultBuilder(args)
        .UseWolverine(options =>
        {
            options.LocalQueue("test");
        })
        .ConfigureAppConfiguration((context, builder) =>
        {

            ConfigurationContainer.Configuration = builder
                .Build();
        })
        .ConfigureServices(services =>
        {
            // Configure RCommon
            services.AddRCommon()
                .WithEventHandling<WolverineEventHandlingBuilder>(eventHandling =>
                {
                    eventHandling.AddProducer<PublishWithWolverineEventProducer>();
                    eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
                });

        }).Build();

    await host.StartAsync();
}
catch (Exception ex)
{
    Console.WriteLine(ex.ToString());

}


```

### Implementation

{% hint style="info" %}
The implementation details for how you produce and subscribe to events will not change due to a consistent producer/subscriber API in RCommon.
{% endhint %}

#### Producing the Event

```csharp
Console.WriteLine("Example Starting");
var eventProducers = host.Services.GetServices<IEventProducer>();
var testEvent = new TestEvent(DateTime.Now, Guid.NewGuid());

foreach (var producer in eventProducers)
{
    Console.WriteLine($"Producer: {producer}");
    await producer.ProduceEventAsync(testEvent);
}

Console.WriteLine("Example Complete");
Console.ReadLine();
```

#### Subscribing to the Event

```csharp
using RCommon.EventHandling.Subscribers;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Messaging.Wolverine
{
    public class TestEventHandler : ISubscriber<TestEvent>
    {
        public TestEventHandler()
        {
                
        }

        public async Task HandleAsync(TestEvent notification, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("I just handled this event {0}", new object[] { notification.ToString() });
            await Task.CompletedTask;
        }
    }
}
```



{% hint style="info" %}
Try running some examples
{% endhint %}

{% content-ref url="../../../examples/event-handling-wolverine.md" %}
[event-handling-wolverine.md](../../../examples/event-handling-wolverine.md)
{% endcontent-ref %}

