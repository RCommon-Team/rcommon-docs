---
description: >-
  Query Bus implemented by RCommon to Assist with Segregation of Write and Read
  Models via CQRS Pattern
---

# Queries

### Overview

```mermaid
sequenceDiagram
    participant Client
    participant API Endpoint
    participant Query Bus
    participant Read Model
    participant Reporting Store

    Client->>+API Endpoint: Request
    API Endpoint->>+Query Bus: Query
    Query Bus->>+Reporting Store: Load
    Reporting Store-->>-Query Bus: Read Model
    Query Bus-->>-API Endpoint: Return result
    API Endpoint-->>-Client: Return result
```



### Mechanics of a Query in CQRS

#### Query

Coming soon...

#### Query Validation

Coming soon...

#### Query Bus

Coming soon...

#### Query Handler

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
