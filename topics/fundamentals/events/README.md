---
description: Event handling fundamentals in RCommon.
---

# Events

### Overview

Event handling can be somewhat ambiguous these days. There are so many kinds of events representing so many different scenarios in application programming. In addition to the types of events available, there are now many ways to raise, transmit, and ultimately handle them. RCommon attempts to delineate the types of events that an application can use, and ultimately categorize them by their intent.&#x20;



### Abstract

Events are relatively simple things. They are objects which are created, transmitted by a [producer](producers.md), and then consumed by a [subscriber](subscribers.md).&#x20;

### In Memory vs. Distributed Events

At the highest level, RCommon categorizes events by the way they are consumed. They are either received from some form of [event bus](../../patterns/event-bus/), or a [message bus](../../patterns/message-bus/). In that respect, events are either "in memory" or "distributed". In general, event buses are a means of raising (producing) events in memory/in-process where the event bus then finds an event handler (subscriber) that may be subscribed to that event. Message buses work in a similar way, except the event is raised, serialized into a binary or json format and then transmitted across a network to a message queue via message bus. From there, a listener from another application picks up that message from the queue and then raises it in the same manner that an event bus would - ultimately finding subscribers for that event in that application as well.&#x20;



### Some Opinions

Ok, now for some opinions on event handling. In order to simplify how a developer interacts with events across architectural and design patterns, we had to make some design choices. Some of these are hard constraints while others are a form of guidance.&#x20;

#### Immutability - Guidance

Ideally, events should represent something that has already happened. As such, they should be considered immutable - that is to say they should not be changed in their intent. While there is nothing in RCommon that forces this behavior, your applications will likely suffer some "unintended" consequences should you decide to allow an event state to be altered once generated.&#x20;

#### Serialization - Guidance

You will notice that all events in RCommon are marked with the "ISerializable" interface. Modern applications utilize lots of different kinds of events i.e. domain events, integration events, etc. Given the direction of application architecture, our opinion is that you should consider making all of your events serializable. While there is nothing in RCommon that forces serialization and your "in memory" events raised through various in-memory [event buses](../../patterns/event-bus/) will work just fine, more and more architectural patterns will require serialization and you may find that it will be difficult to adapt your application if you don't start with serializability of your events in mind. As one of the tenants of RCommon is to future proof applications, our [vision ](../../../getting-started/roadmap.md)of RCommon is one that sees all events as needing to be serializable to assist with future implementations of event durability, event sourcing, GRPC, actor models, and high-volume event streams.&#x20;

#### Marker Interfaces - A Rule

RCommon utilizes marker interfaces in event handling. This is to act as a guard rail and tagging mechanism for event routing, simplifying dependency injection, and to ultimately simplify what you can and cannot do with events within RCommon. This is in contrast to using attributes or nothing at all. Our feeling is that some guardrails are ok, so long as we are favoring composition over inheritance - an object oriented principle. Key marker interfaces:

[_ISerializableEvent_](#user-content-fn-1)[^1]

This is a required interface for any event that is can be produced or subscribed to in RCommon. While your event does not technically need to be serializable, you will be [limited ](./#serialization-guidance)to building applications which run in-process. This is not an RCommon constraint but rather a constraint of most distributed application architectures. &#x20;

```csharp
using RCommon.EventHandling;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace Examples.Messaging.MassTransit
{
    public class TestEvent : ISerializableEvent
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

_IAsyncEvent_

This implements ISerializableEvent and identifies the event as one that should be processed asynchronously without regard for sequential order. This is the most efficient type of event.&#x20;

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

_ISyncEvent_

This also implements ISerializableEvent and identifies the event as one that should be processes using a first-in-first-out (FIFO) strategy with strong regard for order and sequencing. &#x20;

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







[^1]: 
