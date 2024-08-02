---
description: Asynchronous event handling in RCommon
---

# Asynchronous Events



{% hint style="info" %}
Synchronous and Asynchronous events should not be confused with parallel task processing which governs thread management in .NET. e.g. await SomeOperationAsync();

Synchronous and Asynchronous events have more to do with the order in which the events are processed.&#x20;
{% endhint %}



### Implementation

It is very simply mark an event as asynchronous and there is no specific implementation required. We simply require the IAsyncEvent marker interface:

```csharp
using RCommon.EventHandling;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Messaging.MassTransit
{
    public class TestEvent : IAsyncEvent
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

