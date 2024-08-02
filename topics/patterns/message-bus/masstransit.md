---
description: MassTransit message bus event handling implementation in RCommon
---

# MassTransit

### Overview

[MassTransit ](https://masstransit-project.com/)is a framework that abstracts away the complexities of messaging queues and distributed computing at large. It is perhaps one of the best message bus implementations in the .NET ecosystem and is incredibly feature rich.&#x20;

[MassTransit is very well documented](https://masstransit-project.com/) and well supported by the open source community.&#x20;



### Configuration

```csharp
using Examples.Messaging.MassTransit;
using MassTransit;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling;
using RCommon.EventHandling.Producers;
using RCommon.MassTransit;
using RCommon.MassTransit.Producers;
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
                        .WithEventHandling<MassTransitEventHandlingBuilder>(eventHandling =>
                        {
                            // This is a wrapper for MassTransit configuration
                            eventHandling.UsingInMemory((context, cfg) =>
                            {
                                cfg.ConfigureEndpoints(context);
                            });

                            // A simple and consistent interface for adding producers and subscribers
                            eventHandling.AddProducer<PublishWithMassTransitEventProducer>();
                            eventHandling.AddSubscriber<TestEvent, TestEventHandler>();
                        });
                    services.AddHostedService<Worker>();
                }).Build();




    await host.RunAsync();
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
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    while (!stoppingToken.IsCancellationRequested)
    {
        Console.WriteLine("Example Starting");
        var eventProducers = _serviceProvider.GetServices<IEventProducer>();
        var testEvent = new TestEvent(DateTime.Now, Guid.NewGuid());

        foreach (var producer in eventProducers)
        {
            Console.WriteLine($"Producer: {producer}");
            await producer.ProduceEventAsync(testEvent);
        }
        await Task.Delay(5000, stoppingToken);
        
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

namespace Examples.Messaging.MassTransit
{
    public class TestEventHandler : ISubscriber<TestEvent>
    {
        public TestEventHandler()
        {
                
        }
        
        public async Task HandleAsync(TestEvent notification, CancellationToken cancellationToken = default)
        {
            Console.WriteLine("I just handled this event {0}", new object[] { notification.ToString() });
            Console.WriteLine("Example Complete");
            await Task.CompletedTask;
        }
    }
}

```



{% hint style="info" %}
Try running some examples
{% endhint %}

{% content-ref url="../../../examples/event-handling-masstransit.md" %}
[event-handling-masstransit.md](../../../examples/event-handling-masstransit.md)
{% endcontent-ref %}
