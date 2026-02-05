---
description: >-
  A working example of in-memory event handling using RCommon's built-in event
  bus with the producer/subscriber pattern.
---

# Event Handling: In Memory

### Overview

This example demonstrates how to use RCommon's simple in-memory event bus for event-driven communication within your application. The in-memory event bus is dependency-free and implements the observer pattern through pub/sub.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/EventHandling/Examples.EventHandling.InMemoryEventBus).
{% endhint %}

### Configuration

```csharp
using Examples.EventHandling.InMemoryEventBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling;
using RCommon.EventHandling.Producers;
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
                        .WithEventHandling<InMemoryEventBusBuilder>(eventHandling =>
                        {
                            eventHandling.AddProducer<PublishWithEventBusEventProducer>();
                            eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
                        });

                }).Build();

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

Events implement `ISyncEvent` to indicate they are synchronous events handled in-process.

```csharp
using RCommon.EventHandling;
using RCommon.Models.Events;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.EventHandling.InMemoryEventBus
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

Event handlers implement `ISubscriber<TEvent>` which provides a consistent API regardless of the underlying event bus implementation.

```csharp
using RCommon.EventHandling.Subscribers;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.EventHandling.InMemoryEventBus
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

{% content-ref url="../topics/patterns/event-bus/in-memory.md" %}
[in-memory.md](../topics/patterns/event-bus/in-memory.md)
{% endcontent-ref %}
