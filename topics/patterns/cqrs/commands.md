---
description: >-
  Command Bus implemented by RCommon to Assist with Segregation of Write and
  Read Models via CQRS Pattern
---

# Commands

### Overview

```mermaid
sequenceDiagram
  participant Client as Client
  participant API Endpoint as API Endpoint
  participant Command Service as Command Bus
  participant Persistence as Persistence Layer
  participant Data Store as Data Store

  Client ->>+ API Endpoint: Request
  API Endpoint ->> API Endpoint: Deserialize request
  API Endpoint ->>+ Command Service: Command
  Command Service ->>+ Persistence: Execute
   Persistence ->>+ Data Store: Store Changes
  Data Store -->>- Command Service: Return result
  Command Service -->>- API Endpoint: Return result
  API Endpoint -->>- Client: Return result

```



### Mechanics of a Command in CQRS

#### Command

Coming soon...

#### Command Validation

Coming soon...

#### Command Bus

Coming soon...

#### Command Handler

Coming soon...



### Putting It All Together

Coming soon...



### Examples

{% content-ref url="../../../examples/cqrs.md" %}
[cqrs.md](../../../examples/cqrs.md)
{% endcontent-ref %}

{% content-ref url="../../../examples/clean-architecture.md" %}
[clean-architecture.md](../../../examples/clean-architecture.md)
{% endcontent-ref %}
