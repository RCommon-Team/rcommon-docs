---
description: >-
  Persistence through the RCommon Framework ensures a consistent programming
  experience no matter what the underlaying data provider may be.
---

# Persistence

Persistence is a subject near and dear to our collective heart. As it is involved in most modern applications be it transactional databases, or document databases, we a have near daily need to persist and retrieve information from various data [providers](broken-reference).&#x20;

Most of our persistence strategies revolve around the need for the [unit of work (UoW)](transactions/) pattern in conjunction with the [repository pattern](repository/) with or without a requirement for distributed transactions. If you have a simple application with basic persistence needs, then feel free to skip all this and move on. But for larger applications, and especially enterprise applications - the need for persistence and the use cases we solve for below is an absolute must have.&#x20;

## Use Cases for Persistence

* Use the Unit of Work pattern but not be tied to a specific ORM or tool.
* Built-in support for [domain events](../../fundamentals/events/synchronous-events.md) and [distributed events](../../fundamentals/events/asynchronous-events.md).
* Built-in support for [change tracking](broken-reference) via entity base classes.
* Consistent programming interface no matter which ORM you're using.&#x20;
* Freedom to create your own Repository pattern implementation and still utilize everything RCommon persistence offers.&#x20;
