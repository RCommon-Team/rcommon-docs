---
description: RCommon's implementation of provider specific transactions.
---

# Native Transactions

Native transactions can be described as transactions which occur against a single data provider such as SQL Server. The unit of work pattern that we've implemented essentially wraps the TransactionScope class which allows us to loosely couple transactions and [persistence](../).&#x20;
