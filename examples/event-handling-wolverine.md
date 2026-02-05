---
description: >-
  A working example of event handling using Wolverine as the messaging framework
  through RCommon's consistent producer/subscriber API.
---

# Event Handling: Wolverine

### Overview

This example demonstrates how to use Wolverine as the messaging framework for event handling. Wolverine provides a lightweight, high-performance messaging framework with support for local queues and external transports. This example uses a local queue for simplicity.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/Messaging/Examples.Messaging.Wolverine).
{% endhint %}

### Configuration

```csharp
using Examples.Messaging.Wolverine;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling;
using RCommon.EventHandling.Producers;
using RCommon.Wolverine;
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

#### The Event

```csharp
using RCommon.Models.Events;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Messaging.Wolverine
{
    public class TestEvent : ISyncEvent
    {
        public TestEvent(DateTime dateTime, Guid guid)
        {
            DateTime = dateTime;
            Guid = guid;
        }

        public TestEvent()
        {

        }

        public DateTime DateTime { get; }
        public Guid Guid { get; }
    }
}
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

### Learn More

{% content-ref url="../topics/patterns/message-bus/wolverine.md" %}
[wolverine.md](../topics/patterns/message-bus/wolverine.md)
{% endcontent-ref %}
