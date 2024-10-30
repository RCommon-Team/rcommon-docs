---
description: Caching persistence data in RCommon
---

# Persistence Caching

Occasionally there is the need to cache data that is coming from your persistence layer so that you can avoid hitting your data store repeatedly for data that is commonly used. RCommon provides a proxy for all repository implementations available in RCommon which allows you to use the repository pattern in conjunction with caching services.&#x20;



### Configuration

{% hint style="info" %}
Note the usage of "ef.AddInMemoryPersistenceCaching" in the configuration below. This will configure the caching repository interfaces for you to use.&#x20;
{% endhint %}

```csharp
var host = Host.CreateDefaultBuilder(args)
                .ConfigureServices(services =>
                {
                    // Configure RCommon
                    services.AddRCommon()
                        .WithPersistence<EFCorePerisistenceBuilder>(ef => // Repository/ORM configuration. We could easily swap out to Linq2Db without impact to domain service up through the stack
                        {
                            // Add all the DbContexts here
                            ef.AddDbContext<TestDbContext>("TestDbContext", ef =>
                            {
                                ef.UseSqlServer(config.GetConnectionString("TestDbContext"));
                            });
                            ef.AddInMemoryPersistenceCaching(); // This gives us access to the caching repository interfaces/implementations
                        })
                        // You still need to configure this as well!
                        .WithMemoryCaching<InMemoryCachingBuilder>(cache =>
                        {
                            cache.Configure(x =>
                            {
                                x.ExpirationScanFrequency = TimeSpan.FromMinutes(1);
                            });
                        });
                }).Build();

```

{% hint style="danger" %}
It is still important that you configure caching in addition to adding persistence caching since that is what implements the caching abstractions.
{% endhint %}

### Usage

```csharp
public class TestApplicationService : ITestApplicationService
{
    private readonly ICachingGraphRepository<Customer> _customerRepository;

    public TestApplicationService(ICachingGraphRepository<Customer> customerRepository)
    {
        _customerRepository = customerRepository;
        _customerRepository.DataStoreName = "TestDbContext";
    }

    public async Task<ICollection<Customer>> GetCustomers(object cacheKey)
    {
        return await _customerRepository.FindAsync(cacheKey, x => x.LastName == "Potter");
    }
}
```

### Behind the Scenes

This is what is happening behind the scenes in the caching repository proxy

<pre class="language-csharp"><code class="lang-csharp">public class CachingGraphRepository&#x3C;TEntity> : ICachingGraphRepository&#x3C;TEntity>
    where TEntity : class, IBusinessEntity
{
    private readonly IGraphRepository&#x3C;TEntity> _repository;
    private readonly ICacheService _cacheService;

    public CachingGraphRepository(IGraphRepository&#x3C;TEntity> repository, ICommonFactory&#x3C;PersistenceCachingStrategy, ICacheService> cacheFactory)
    {
        _repository = repository;
        _cacheService = cacheFactory.Create(PersistenceCachingStrategy.Default);
    }
<strong>
</strong><strong>    public async Task&#x3C;IPaginatedList&#x3C;TEntity>> FindAsync(object cacheKey, Expression&#x3C;Func&#x3C;TEntity, bool>> expression, Expression&#x3C;Func&#x3C;TEntity,
</strong>        object>> orderByExpression, bool orderByAscending, int pageNumber = 1, int pageSize = 0, CancellationToken token = default)
    {
        var data = await _cacheService.GetOrCreateAsync(cacheKey, 
            async () => await _repository.FindAsync(expression, orderByExpression, orderByAscending, pageNumber, pageSize, token));
        return await data;
    }
    
    public async Task&#x3C;IPaginatedList&#x3C;TEntity>> FindAsync(object cacheKey, IPagedSpecification&#x3C;TEntity> specification, CancellationToken token = default)
    {
        var data = await _cacheService.GetOrCreateAsync(cacheKey,
            async () => await _repository.FindAsync(specification, token));
        return await data;
    }
    
    public async Task&#x3C;ICollection&#x3C;TEntity>> FindAsync(object cacheKey, ISpecification&#x3C;TEntity> specification, CancellationToken token = default)
    {
        var data = await _cacheService.GetOrCreateAsync(cacheKey,
             async () => await _repository.FindAsync(specification, token));
        return await data;
    }
    
    public async Task&#x3C;ICollection&#x3C;TEntity>> FindAsync(object cacheKey, Expression&#x3C;Func&#x3C;TEntity, bool>> expression, CancellationToken token = default)
    {
        var data = await _cacheService.GetOrCreateAsync(cacheKey,
            async () => await _repository.FindAsync(expression, token));
        return await data;
    }
</code></pre>
