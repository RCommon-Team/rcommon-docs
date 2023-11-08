---
description: Object Relational Mapper (ORM) Abstraction
---

# ORM Abstraction

## Use Case

You love using the repository pattern but you've been around long enough to watch [Linq to SQL](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/linq/), several breaking changes of Entity Framework, and countless  flavors of Micro-ORMs go by and changing your data access layer one more time may free you from sanity. Or maybe you just want to learn from us - that deprecating large data access APIs on a semi regular basis is just the way it is. Either way, we welcome you to the relative calm of existence of [persistence abstraction](../../../v1/fundamentals/persistence/). As it turns out, there aren't that many ways to use ORMs, so why should your application suffer is there is a major update to your favorite ORM? The answer is "it shouldn't", and this is why we built our persistence abstraction layer.
