---
description: Roadmap for RCommon
---

# Roadmap

### Version 1.0.0

The initial version of RCommon was redesigned from the original NCommon project privately, but under the Apache 2.0 license in 2008. That version was not publicly released but we've built on the success of that production-level code.&#x20;

### Version 1.0.1

Our first public release published in late 2022. [GitHub "1.0.1.75"](https://github.com/RCommon-Team/RCommon) is where you can find the code for this release[. ](https://github.com/RCommon-Team/RCommon/tree/v1.0.1.75)We will continue to support 1.0.1.x as long as .NET 6 is supported.

### Version 1.0.2

This release supports .NET 7 but does not add any additional features/enhancements beyond that. The [GitHub "Main" branch](https://github.com/RCommon-Team/RCommon/tree/main) represents the current state. We will continue to support 1.0.2.x as long as .NET 7 is supported.&#x20;

### Version 2.0

The first public release was developed mostly in private in 2008 and was used mainly as persistence infrastructure in enterprise applications. We've spent so long getting to the first public release that there have been many advancements in the development of common design and architectural patterns. Since 2013 - event driven applications, and microservices have seen a great deal of interest from the development community.  As such, we've built up a laundry list of new features, but also some opportunities for refinement to provide a foundation for building applications for years to come. Given the scope of the changes to foundational layers, we feel a major version bump is warranted.  From there, we'll introduce new features more frequently using minor versions. This version has major breaking changes due to the nature of the updates so please be cautious with the upgrade.&#x20;

#### Features / Enhancements

* .NET 6, 7, 8+ Support
* Simplified Dependency Injection API
* Redesigned Unit of Work
* Mediator Pattern
  * [MediatR ](https://github.com/jbogard/MediatR)Implementation (New)
* CQRS Pattern (New)
  * Commands & Command Handlers
  * Queries & Query Handlers
* Validation (New)
  * [FluentValidation ](https://docs.fluentvalidation.net/en/latest/)(New)
  * Validation on Command/Query Bus pipeline
  * Validation on Mediator pipeline
* Event Handling & Messaging
  * In Memory Event Bus (New)
  * In Memory Producing/Subscribing: [MediatR](https://github.com/jbogard/MediatR) (New)
  * Event Producing/Subscribing: [MassTransit](https://masstransit.io/) (Enhanced)
  * Event Producing/Subscribing: [Wolverine](https://wolverine.netlify.app/) (New)
* More comprehensive persistence infrastructure
  * Persistence: [Linq2Db](https://linq2db.github.io/index.html) (New)
* Improved Unit Testing Foundation
* Examples
  * Leave Requestion Application (Enhanced)

### Version 2.1.0 (Current)

* JSON Serialization (New)
  * [Newtonsoft JSON.NET](https://www.newtonsoft.com/json)
  * System.Text.Json
* Caching (New)
  * [Redis](https://redis.io/)/[Valkey ](https://valkey.io/)Caching
  * In Memory Caching
* Persistence
  * Cached Queries (New)

### Version 2.2.\*&#x20;

* .NET 9 Support
* Identity
  * Entity Framework Core Identity Providers
  * Linq2Db Identity Providers
  * Dapper Identity Providers
* Multitenancy
  * Database per Tenant
  * Multitenant Database
  * Host Strategy
  * Header Strategy
  * Path Strategy
* Asp.Net Core
  * API Key Authentication
  * Claims based Authorization&#x20;



### Potential Future Features

* Supabase Repository
* Concurrency Support
* CQRS Pattern
  * Read Model Abstractions
  * Auto-Http API for Commands and Queries
* Event Sourcing Abstractions
  * EventStore
  * Event State
  * Read Model Projection
* Soft Deleting
* Domain Driven Design Support (New)
* ETL Support
* Localization Support
* Blob Abstractions
* Template Abstractions
* AI
  * Agent to Agent (A2A) Support
  * Model Context Protocol Support





