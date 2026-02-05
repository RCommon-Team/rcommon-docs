---
description: >-
  A working example of event handling using MediatR as the underlying transport
  through RCommon's consistent producer/subscriber API.
---

# Event Handling: MediatR

### Overview

This example demonstrates how to use MediatR as the event handling transport while still using RCommon's consistent producer/subscriber API. Switching from the in-memory event bus to MediatR requires only a configuration change - the event and handler implementations remain the same.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/EventHandling/Examples.EventHandling.MediatR).
{% endhint %}

### Configuration

```csharp
using Examples.EventHandling.MediatR;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling.Producers;
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
                        .WithEventHandling<MediatREventHandlingBuilder>(eventHandling =>
                        {
                            eventHandling.AddProducer<PublishWithMediatREventProducer>();

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

```csharp
using MediatR;
using RCommon.Mediator;
using RCommon.Mediator.Subscribers;
using RCommon.Models.Events;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.EventHandling.MediatR
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

namespace Examples.EventHandling.MediatR
{
    public class TestEventHandler : ISubscriber<TestEvent>
    {
        public TestEventHandler()
        {

        }

        public async Task HandleAsync(TestEvent notification, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("{0} just handled this event {1}", new object[] { this.GetGenericTypeName(), notification });
            await Task.CompletedTask;
        }
    }
}
```

### Learn More

{% content-ref url="../topics/patterns/event-bus/mediatr.md" %}
[mediatr.md](../topics/patterns/event-bus/mediatr.md)
{% endcontent-ref %}
