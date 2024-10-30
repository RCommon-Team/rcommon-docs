---
description: Optional caching services available in RCommon
---

# Caching Services

Generally speaking, there are usually a couple of caching solutions utilized in enterprise applications: In-Memory-Cache and Distributed-Memory-Cache. In-Memory caching is by far the most efficient but occupies valuable memory in your application server and does not scale well. Distributed-Memory caching is less efficient by scales much more easily when paired with providers such as Redis or Valkey. RCommon utilizes some key services to assist with implementing caching no matter which solution(s) you choose.&#x20;
