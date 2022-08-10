---
description: Policy based exception handling for enterprise applications.
---

# Exception Handling

{% hint style="danger" %}
Beware, that this library is likely to change substantially in the near future!
{% endhint %}

Admittedly, we are beginning to re-think this area of RCommon. As with all things in RCommon, RCommon represents abstractions over commonly used patterns - in this case, policy based exception handling. There has only been one implementation of exception handling and that has been [Microsoft's Exception Handling Application Blocks](https://github.com/EnterpriseLibrary/exception-handling-application-block) which has been relatively stagnant in the open source community for quite some time.&#x20;

Although we love policy-based exception handling, logging (and logging libraries) have become first class citizens in .NET 6 and given how modern applications behave, we are re-examining this abstraction. There are plenty of fluent programming libraries out there that could easily accommodate policy-based exception handling in conjunction with rich exception handlers which would better serve the community.&#x20;

If you have ideas on how to better implement Exception Handling, [then let us know!](https://github.com/RCommon-Team/RCommon/discussions/64)
