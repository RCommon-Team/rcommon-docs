---
description: Transactions implemented via the Unit of Work pattern within RCommon.
---

# Transactions

Transactions are a complex topic within the realm of enterprise applications. The topic has been made even more complicated when [TransactionScope stopped support transactions across multiple database providers](https://github.com/dotnet/runtime/issues/715). [While .NET 7+ attempts to reconcile this problem](https://github.com/dotnet/runtime/issues/71769), RCommon is also taking a shot at it.&#x20;

Ultimately, RCommon utilizes the [Unit of Work pattern](unit-of-work.md) to accomplish both native (localized provider specific) transactions as well as distributed transactions. The unit of work gives us fine grained control over what is happening with transactions in our application. As [mediatr ](https://github.com/jbogard/MediatR)is a first class citizen of RCommon, we are also able to use [Mediator Behavior pipelines](broken-reference) to loosely couple our unit of work behavior.&#x20;

{% content-ref url="native-transactions.md" %}
[native-transactions.md](native-transactions.md)
{% endcontent-ref %}

{% content-ref url="distributed-transactions.md" %}
[distributed-transactions.md](distributed-transactions.md)
{% endcontent-ref %}
