---
description: Roadmap for RCommon
---

# Roadmap

### Version 1.0.0

The initial version of RCommon was redesigned from the original NCommon project privately, but under the Apache 2.0 license. That version was not publicly released but we've built on the success of that production-level code.&#x20;

### Version 1.0.1&#x20;

[Version 1.0.1 Kanban Board](https://github.com/RCommon-Team/RCommon/projects/1)

Our first public release published in late 2022. [GitHub "1.0.1.75"](https://github.com/RCommon-Team/RCommon) is where you can find the code for this release[. ](https://github.com/RCommon-Team/RCommon/tree/v1.0.1.75)We will continue to support 1.0.1.x as long as .NET 6 is supported.

### Version 1.0.2 (Current)

This release supports .NET 7 but does not add any additional features/enhancements beyond that. The [GitHub "Main" branch](https://github.com/RCommon-Team/RCommon/tree/main) represents the current state. We will conintue to support 1.0.2.x as long as .NET 7 is supported.&#x20;

### Version 2.0 (In Testing)

We've spent so long getting to the first public release that there is a laundry list of items that need to get addressed - several new features, but also some opportunities for refinement. Given the scope of the changes to foundational layers, we feel a major version bump is warranted.  From there, we'll introduce new features more frequently using minor versions.&#x20;

#### Planned Features / Enhancements

* Simplified Dependency Injection API
* Redesigned Unit of Work implementation
* More comprehensive event bus infrastructure
  * Distributed Event Publishing: [MassTransit](https://masstransit.io/) (Enhanced)
  * Distributed Event Publishing: [Wolverine](https://wolverine.netlify.app/) (New)
* More comprehensive persistence infrastructure
  * Persistence: [Linq2Db](https://linq2db.github.io/index.html) (New)
* Improved Unit Testing Foundation

### Version 2.1

Up to this point, there has been nothing preventing developers from building applications using domain driven design (DDD). But there are a few hiccups to getting there: the inability to delineate between transactional and non-transactional domain events, and having to roll your own aggregate root. This version attempts to reconcile all that!

* CRUD Repositories Document DBs
  * MongoDB
* Transactional and non-transactional domain events
* Aggregate root support
* DDD Sample
* Soft Deleting
* Multitenancy
* Concurrency Support

### Version 2.2

Building upon a solid and fairly comprehensive set of persistence, and domain driven design, our next focus will be on layering in support for event driven applications.&#x20;

* Event Sourcing Abstractions
  * EventStore Support
  * MongoDB Support
  * Elastic Support
  * Transactional DB (SQL) Support
  * Read Model Projection Support
* Microservices Sample

### Version 2.3

Filling in some gaps with various implementations that are commonly used.&#x20;

* Localization Support
* Blob Abstractions
* Template Abstractions
* Identity Extensions

### Future Versions (Conceptual)

Implement abstractions needed for Actor model implementations.&#x20;

* Implement Z.Extensions and deprecate all other foundational extensions methods (eliminate code bloat and increase unit testability)
* Dapr Support
* Akka.NET Support
* Orleans Support?
* Proto.Actor Support?

Implement abstractions for Machine Learning implemenations

* Sagemaker
* Azure MLOps

