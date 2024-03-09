# Claims

### Configuration

```csharp
// Add RCommon services
builder.Services.AddRCommon()
    .WithClaimsAndPrincipalAccessor();
```

### Usage

```csharp
public class MyService
{
    private readonly ICurrentUser _currentUser;

    public MyService(ICurrentUser currentUser)
    {
        _currentUser = currentUser;
    }
    
    public Claim[] GetClaims()
    {
        return _currentUser.GetAllClaims();
    }
}
```
