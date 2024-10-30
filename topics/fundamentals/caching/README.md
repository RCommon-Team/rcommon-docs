---
description: Caching abstractions in RCommon
---

# Caching

[Caching abstractions](https://learn.microsoft.com/en-us/dotnet/core/extensions/caching) have been generally available on .NET for quite some time and most major providers have already adapted their implementations to those abstractions. RCommon utilizes those abstractions in dynamic parameter caching, expression caching, and persistence caching but we needed to implement a series of wrappers for the dependency injection/configuration of those providers so that we could provide a seamless experiences for integrating those services throughout the various libraries that are capable of using caching to improve performance.&#x20;



### Configuration

Bootstrapping caching into RCommon is very straight forward and should feel consistent with the configuration experience of the caching abstractions provided by .NET 8+.

```csharp
var host = Host.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((context, builder) =>
            {

                ConfigurationContainer.Configuration = builder
                    .Build();
            })
            .ConfigureServices(services =>
            {
                // Configure RCommon
                services.AddRCommon()
                    .WithJsonSerialization<JsonNetBuilder>() // Distributed memory caching requires serialization
                    .WithMemoryCaching<InMemoryCachingBuilder>(cache =>
                    {
                        cache.Configure(x =>
                        {
                            x.ExpirationScanFrequency = TimeSpan.FromMinutes(1);
                        });
                    })
                    .WithDistributedCaching<DistributedMemoryCacheBuilder>(cache =>
                    {
                        cache.Configure(x =>
                        {
                            x.ExpirationScanFrequency = TimeSpan.FromMinutes(1);
                        });
                    });

                services.AddTransient<ITestApplicationService, TestApplicationService>();
                
            }).Build();

Console.WriteLine("Example Starting");
var appService = host.Services.GetRequiredService<ITestApplicationService>();

// In Memory Cache
appService.SetMemoryCache("test-key", new TestDto("test data 1"));
var testData1 = appService.GetMemoryCache("test-key");

// In Memory Distributed Cache
appService.SetDistributedMemoryCache("test-key", typeof(TestDto), new TestDto("test data 2"));
var testData2 = appService.GetDistributedMemoryCache("test-key");

Console.WriteLine(testData1.Message);
Console.WriteLine(testData2.Message);

Console.WriteLine("Example Complete");
```

{% hint style="info" %}
Note the usage of the existing .NET caching abstractions IMemoryCache, and IDistributedCache
{% endhint %}

```csharp
public class TestApplicationService : ITestApplicationService
{
    private readonly IMemoryCache _memoryCache;
    private readonly IDistributedCache _distributedCache;
    private readonly IJsonSerializer _serializer;

    public TestApplicationService(IMemoryCache memoryCache, IDistributedCache distributedCache, IJsonSerializer serializer)
    {
        _memoryCache = memoryCache;
        _distributedCache = distributedCache;
        _serializer = serializer;
    }

    public void SetMemoryCache(string key, TestDto data)
    {
        _memoryCache.Set<TestDto>(key, data);
    }

    public TestDto GetMemoryCache(string key)
    {
        return _memoryCache.Get<TestDto>(key);
    }

    public void SetDistributedMemoryCache(string key, Type type, object data)
    {
        _distributedCache.Set(key, Encoding.UTF8.GetBytes(_serializer.Serialize(data, type)));
    }

    public TestDto GetDistributedMemoryCache(string key)
    {
        var cache = _distributedCache.Get(key);
        return _serializer.Deserialize<TestDto>(Encoding.UTF8.GetString(cache));
    }
}
```

