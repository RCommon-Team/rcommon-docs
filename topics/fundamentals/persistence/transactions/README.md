---
description: Transactions implemented via the Unit of Work pattern within RCommon.
---

# Transactions

Transactions are a complex topic within the realm of enterprise applications. The topic has been made even more complicated when [TransactionScope stopped support transactions across multiple database providers](https://github.com/dotnet/runtime/issues/715). [While .NET 6+ attempts to reconcile this problem](https://github.com/dotnet/runtime/issues/71769), RCommon is also taking a shot at it.&#x20;

The unit of work gives us fine grained control over what is happening with transactions in our application.&#x20;

{% content-ref url="native-transactions.md" %}
[native-transactions.md](native-transactions.md)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}
