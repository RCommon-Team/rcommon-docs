---
description: Transactional (Deferred) Events and the strategy behind them in RCommon
---

# Transactional Events

### Overview

Transactional events (aka Deferred events) are events which are persisted to a data store and held in a queue until some criteria of your application has been met. When the target criteria has been met, then a mechanism notifies the queue that it should send out all the events to their subscribers.&#x20;



```mermaid
sequenceDiagram
  participant Application
  participant Domain 
  participant Event Bus
  participant Unit of Work
  participant Data Store

  Application ->>+ Domain : Command
Domain ->>+ Unit of Work: Track Events
Unit of Work ->>+ Data Store: Store Changes
  Data Store ->>+ Unit of Work: Return Success
Unit of Work ->>+ Event Bus: Produce Events
  Event Bus -->>- Application: Handle Events

```
