---
description: Roadmap for RCommon
---

# Roadmap

### Version 1.0.0

The initial version of RCommon was redesigned from the original NCommon project privately, but under the Apache 2.0 license. That version was not publicly released but we've built on the success of that production-level code.&#x20;

### Version 1.0.1

[Version 1.0.1 Kanban Board](https://github.com/RCommon-Team/RCommon/projects/1)

Our first public release scheduled for late 2022. [GitHub "main"](https://github.com/RCommon-Team/RCommon) represents the current state of this release.&#x20;

{% hint style="info" %}
We are currently developing the final items before publishing 1.0.1. Most of the functionality has been running in a production capacity for various projects our team has been using for more than 5 years. The last of the work centers on samples, unit testing, integration testing, migration scripts, and documentation.&#x20;
{% endhint %}

### Version 2.0

We've spent so long getting to the first public release that there is a laundry list of items that need to get addressed - several new features, but also some opportunities for refinement. Given the scope of the changes to foundational layers, we feel a major version bump is warranted.  From there, we'll introduce new features more frequently using minor versions.&#x20;

#### Planned Features / Enhancements

* Simplify Dependency Injection API
* Redesign Unit of Work implementation
* More comprehensive event bus infrastructure
* Improved Unit Testing Foundation
* New Features
  * Improved Unit Testing Foundation
  * MongoDB Support
  * Linq2Db Support

### Version 2.1

Building upon a solid and fairly comprehensive set of persistence, and event based architecture infrastructure, our next focus will be on layering in support for microservice architecture and preparing for explicit domain driven design support.&#x20;

* Event Sourcing Abstractions/support
* Microservices Sample

### Version 2.2

Up to this point, there has been nothing preventing developers from building applications using domain driven design (DDD). But there are a few hiccups to getting there: lack of explicit support for object graphs in repositories, the inability to delineate between transactional and non-transactional domain events, and having to roll your own aggregate root. This version attempts to reconcile all that!

* Transactional and non-transactions domain events
* Aggregate root support
* DDD Sample
* Soft Deleting
* Multitenancy
* Concurrency Support
