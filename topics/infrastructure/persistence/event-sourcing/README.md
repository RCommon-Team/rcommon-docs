---
description: Coming soon... Event Sourcing architecture in RCommon
---

# Event Sourcing

Event Sourcing is relatively new to the persistence conversation as compared to CRUD (create/read/update/delete). Several opinions have formed as to the "right way" to do things in Event Sourcing. Honoring our philosophy, we do not intend to address every facet of proper Event Sourcing architectural concerns. Rather, we allow developers the freedom to address the concerns through the flexibility of our libraries.&#x20;

## The "Right Way"

In our opinion, there is no "right way" to handle Event Sourcing. Every method we've seen has adds complexity and gotchas. However, there are some patterns and technologies that can help.  Before we get there however, it may be instrumental for you to understand how we've arrived at our conclusions. We have taken inspiration from a few different Event Souring projects as well as powerful messaging frameworks. And while Event Sourcing and Event Driven Architecture are completely different things, we find that they often compliment each other. This is the 'readers digest condensed' version in the form of design goals most Event Sourcing projects hope to achieve along with some links to those resources:

* Domain events should be immutable
* Domain events should be first in - first out (FIFO)&#x20;
* Domain events should be serializable&#x20;
* Domain events are not necessarily meant to be distributed events (integration events)
* Avoid Two-phase Commits
* Event stores are immutable and append only
* Projections should be used to alter read model stores
* Subscribers to domain Events should consume those events from a transactional outbox

Ok, a lot to unpack so here are three reference architectures that we'll use to educate you on the pros and cons.&#x20;

### Reference Architectures (in no particular order)

#### #1 - Event Sourcing w/ Event Bus & Transactional Outbox

This architecture utilizes an event bus along with a transactional outbox to mitigate the concerns around consuming domain events from a subsystem (like projection handlers, or message brokers) after they have been persisted to the Event Store.&#x20;

RCommon utilizes an Event Bus to send or publish domain events through their associated event producers. A single domain event may have multiple event producers which all but ensures that a single event cannot be wrapped in an atomic transaction. Instead, we need to rely on the transactional outbox pattern to ensure that any would be subscribers use a persistent queue (like a SQL database) to guarantee delivery to the subscriber. Both the domain event as well as the state of domain event delivery state are persisted in a common database and we are able to rollback if any errors occur during persistence.

<figure><img src="../../../../.gitbook/assets/RCommon - ES - EventBus.svg" alt=""><figcaption><p>Event Sourcing w/ Event Bus and Transactional Outbox</p></figcaption></figure>

Here are some pros and cons:

* Pro: Does not require 3rd party infrastructure to accomplish event storage or subscriptions thereof
* Pro: Observability is greatly improved and tracing problems should be simple
* Pro: It's simple! Domain event and domain event delivery state persistence occur as a common ACID transaction
* Con: Does not scale well. Transactions are relatively expensive, cause thread locks, etc. May be a problem for high volume systems
* Con: Requires some advanced knowledge of outbox patterns. This is mitigated by frameworks like MassTransit which is readily implemented in RCommon - still some assembly required

#### #2 - Event Sourcing w/ Change Data Capture (CDC)

Change Data Capture (CDC) is a fairly mature technology whereby a data source (SQL tables, document database index, etc.) is monitored and changes are broadcasted to a message broker such as RabbitMQ, or Azure Event Hub. The message broker will still need to implement an outbox but this is readily available through production ready Apache licensed software such as Debezium.&#x20;

<figure><img src="../../../../.gitbook/assets/RCommon - ES - CDC.svg" alt=""><figcaption></figcaption></figure>

Here are some pros and cons:

* Pro: CDC is fairly reliable when paired with solutions such as Debezium
* Pro: Scales very well
* Con: Operational challenges! Requires knowledge of implementing CDC on the database provider of your choice, AND knowledge of Debezium or other related tools
* Con: The infrastructure commitment could be considerable

#### #3 - Event Sourcing w/ EventStore DB

EventStore DB is an open source 3-clause BSD licensed document database purpose built for Event Sourcing. It comes with a purpose built client SDK that makes creating Event Sourcing systems much easier. Additionally, it solves the issue two phase commits by allowing subscribers to listen for the finalized domain event after it has been persisted. Essentially it is an Event Sourcing, Outbox implementation, and domain event publisher all in one. This is quintessentially the best and worst thing about it.

<figure><img src="../../../../.gitbook/assets/RCommon - ES - EventStoreDb.svg" alt=""><figcaption></figcaption></figure>

Here are some pros and cons:

* Pro: EventStore DB solves a lot of problems in Event Sourcing
* Con: Operational challenges! You have to know how to manage a one-off document database product that you will almost need to train your team for

### Summary

Ultimately it is up to you how you want to implement Event Sourcing. RCommon alone doesn't solve all the challenges but it doesn't create any new problems either.&#x20;
