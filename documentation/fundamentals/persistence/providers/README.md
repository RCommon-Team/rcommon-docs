---
description: Persistence providers for RCommon.
---

# Providers

RCommon provides (or is planning on providing) a variety of strategies for persistence. Our opinion is that object relational mappers (ORM's) can be divided into three categories with distinct but not exclusive feature sets:

| Feature                             |     Graph Mapper     |      Linq Mapper     |      SQL Mapper      |
| ----------------------------------- | :------------------: | :------------------: | :------------------: |
| Implements IQueryable               | :white\_check\_mark: | :white\_check\_mark: |          :x:         |
| Eager Loading                       | :white\_check\_mark: | :white\_check\_mark: |          :x:         |
| Graph Persistence (nested entities) | :white\_check\_mark: |          :x:         |          :x:         |
| Change Tracking                     | :white\_check\_mark: |          :x:         |          :x:         |
| ACID Transactions                   | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Custom SQL/Queries                  | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Fluent Mapping                      | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Entity State Local/Domain Events    | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Entity State Distributed Events     | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Unit of Work Support                | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |



### Supported Object Relational Mappers (ORMs)

| ORM                    |     Graph Mapper     |      Linq Mapper     |      SQL Mapper      |
| ---------------------- | :------------------: | :------------------: | :------------------: |
| Entity Framework Core  | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Mongo DB Client (soon) | :white\_check\_mark: | :white\_check\_mark: |          :x:         |
| NHibernate (soon)      | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |
| Linq2Db (soon)         |          :x:         | :white\_check\_mark: | :white\_check\_mark: |
| Dapper                 |          :x:         |          :x:         | :white\_check\_mark: |

