---
description: MediatR event handling implementation in RCommon
---

# MediatR

### Overview

Most enterprise applications have a need for a simple in-memory event bus which implements the [observer pattern](https://en.wikipedia.org/wiki/Observer\_pattern) through the [pub/sub pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe\_pattern). RCommon implements a simple dependency-free event bus for this utility but there are some advantages to using MediatR. [MediatR ](../mediator/mediatr/)is a [mediator pattern](../mediator/) implementation which can be used as an event bus. MediatR is a mature library and allows developers to use aspect oriented concepts to manipulate the request pipeline.&#x20;



### Configuration

<pre class="language-csharp"><code class="lang-csharp"><strong>using Examples.EventHandling.MediatR;
</strong>using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using RCommon;
using RCommon.EventHandling.Producers;
using RCommon.MediatR;
using RCommon.MediatR.Producers;
using System.Diagnostics;
using System.Reflection;
using static System.Net.Mime.MediaTypeNames;

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
                        .WithEventHandling&#x3C;MediatREventHandlingBuilder>(eventHandling =>
                        {
                            eventHandling.AddProducer&#x3C;PublishWithMediatREventProducer>();
                            
                            eventHandling.AddSubscriber&#x3C;TestEvent, TestEventHandler>();
                        });

                }).Build();         
}
catch (Exception ex)
{   
    Console.WriteLine(ex.ToString());
    
}
</code></pre>

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

namespace Examples.EventHandling.MediatR
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

{% content-ref url="../../../examples/event-handling-mediatr.md" %}
[event-handling-mediatr.md](../../../examples/event-handling-mediatr.md)
{% endcontent-ref %}
