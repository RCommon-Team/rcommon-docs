---
description: >-
  A working example of event handling using MassTransit as the message bus
  through RCommon's consistent producer/subscriber API.
---

# Event Handling: MassTransit

### Overview

This example demonstrates how to use MassTransit as the message bus for event handling. MassTransit provides durable messaging with support for RabbitMQ, Azure Service Bus, Amazon SQS, and more. This example uses MassTransit's in-memory transport for simplicity, and utilizes a `BackgroundService` to produce events.

{% hint style="info" %}
This is a console application example. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/Messaging/Examples.Messaging.MassTransit).
{% endhint %}

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
                            eventHandling.UsingInMemory((context, cfg) =>
                            {
                                cfg.ConfigureEndpoints(context);
                            });

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

#### The Event

```csharp
using RCommon.Models.Events;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Messaging.MassTransit
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

#### Producing the Event via BackgroundService

Since MassTransit uses a hosted service for message consumption, this example uses a `BackgroundService` to produce events after the host starts.

```csharp
using MassTransit;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using RCommon.EventHandling.Producers;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;

namespace Examples.Messaging.MassTransit
{
    public class Worker : BackgroundService
    {
        private readonly IServiceProvider _serviceProvider;
        private readonly ILogger<Worker> _logger;
        private readonly IHostApplicationLifetime _lifetime;

        public Worker(ILogger<Worker> logger, IServiceProvider serviceProvider, IHostApplicationLifetime lifetime)
        {
            _logger = logger;
            _serviceProvider = serviceProvider;
            _lifetime = lifetime;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            Console.WriteLine("Example Starting");
            var eventProducers = _serviceProvider.GetServices<IEventProducer>();
            var testEvent = new TestEvent(DateTime.Now, Guid.NewGuid());

            foreach (var producer in eventProducers)
            {
                Console.WriteLine($"Producer: {producer}");
                await producer.ProduceEventAsync(testEvent);
            }

            Console.WriteLine("Example Complete");
            _lifetime.StopApplication();
        }
    }
}
```

### Learn More

{% content-ref url="../topics/patterns/message-bus/masstransit.md" %}
[masstransit.md](../topics/patterns/message-bus/masstransit.md)
{% endcontent-ref %}
