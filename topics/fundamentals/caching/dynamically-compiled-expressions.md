---
description: Caching dynamically compiled expressions in RCommon
---

# Dynamically Compiled Expressions

RCommon utilizes dynamically compiled expressions in many areas of CQRS implementations as well as persistence. The inherent overhead associated with reflection used in compiling those expressions is fairly substantial. While this is not an uncommon problem, it can be easily mitigated with caching.&#x20;



### Configuration

{% hint style="info" %}
Note the use of "cache.CacheDynamicallyCompiledExpressions" statement below
{% endhint %}

<pre class="language-csharp"><code class="lang-csharp">var host = Host.CreateDefaultBuilder(args)
            .ConfigureServices(services =>
            {
                // Configure RCommon
                services.AddRCommon()
                    .WithJsonSerialization&#x3C;JsonNetBuilder>() // Distributed memory caching requires serialization
                    .WithMemoryCaching&#x3C;InMemoryCachingBuilder>(cache =>
                    {
                        cache.Configure(x =>
                        {
                            x.ExpirationScanFrequency = TimeSpan.FromMinutes(1);
                        });
                       <a data-footnote-ref href="#user-content-fn-1"> cache.CacheDynamicallyCompiledExpressions();</a>
                    });
            }).Build();
</code></pre>

[^1]: Simply add this statement to configure RCommon to cache dynamically compiled expressions.
