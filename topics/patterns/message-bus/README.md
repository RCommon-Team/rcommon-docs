---
description: Message Bus implementations in RCommon.
---

# Message Bus

### Overview

Fundamentally, there is no difference between how RCommon handles events through a message bus, and how they are handled through an [event bus](../event-bus/). A message that you intend to send through a message bus still comes through the event router, and is then broadcasted through the pipeline of [event producers](../../fundamentals/events/producers.md) that have been registered in dependency injection. The only thing that changes is the implementation of the[ event producer](../../fundamentals/events/producers.md) which uses a client for a message broker such as RabbitMQ, or Amazon SQS - which is ultimately implemented by one of the below [implementations](./#implementations).&#x20;

### Other Considerations

RCommon attempts to simplify many aspects of event/message handling. There are, however, aspects  which are inherently complex albeit necessary to prevent event more complex entanglements of downstream sub-systems which consume your events/messages.&#x20;

#### Two-Phase Commits

Perhaps the most difficult element of many applications that utilize events to communicate with other systems is the need to transmit messages only after local transactions have been committed successfully. As the database storing the data and the message broker are two separate components, making a single transaction that persists an event and publishes it to the broker can be difficult. One of these operations may fail, causing the system to become inconsistent. The most common solution to this problem is the implementation of a [transactional outbox](https://microservices.io/patterns/data/transactional-outbox.html). This is available on both [MassTransit ](https://masstransit.io/documentation/patterns/transactional-outbox)and [Wolverine](https://wolverine.netlify.app/guide/durability/).&#x20;

#### Serialization

Distributing messages through a message bus requires serialization. As such, ensuring that you messages can be serialized is important. The current implementations of message bus have the ability to serialize your messages using various formatters but you'll want to take care to ensure that your objects are serializable so that they don't disrupt the system transmitting/consuming them.&#x20;

### Implementations

{% content-ref url="masstransit.md" %}
[masstransit.md](masstransit.md)
{% endcontent-ref %}

{% content-ref url="wolverine.md" %}
[wolverine.md](wolverine.md)
{% endcontent-ref %}



### Examples

{% content-ref url="../../../examples/event-handling-masstransit.md" %}
[event-handling-masstransit.md](../../../examples/event-handling-masstransit.md)
{% endcontent-ref %}

{% content-ref url="../../../examples/event-handling-wolverine.md" %}
[event-handling-wolverine.md](../../../examples/event-handling-wolverine.md)
{% endcontent-ref %}
