---
description: Synchronous event handling in RCommon.
---

# Synchronous Events

### Overview

Oftentimes, applications require events to be handled in a specific order. Synchronous events are quite simply events which are processed in a synchronous and ordered manner. That is to say, first-in-first-out. This is in contrast to [asynchronous events](asynchronous-events.md) which can be processed efficiently but without respect for order.&#x20;



{% hint style="info" %}
Synchronous and Asynchronous events should not be confused with parallel task processing which governs thread management in .NET. e.g. await SomeOperationAsync();

Synchronous and Asynchronous events have more to do with the order in which the events are processed.&#x20;
{% endhint %}



### Implementation

It is very simply mark an event as synchronous and there is no specific implementation required. We simply require the ISyncEvent marker interface:

```csharp
using RCommon.EventHandling;
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

