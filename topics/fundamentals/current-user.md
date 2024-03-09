---
description: Accessing authenticated user information from RCommon
---

# Current User

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
    
    public void Foo()
    {
        Guid? userId = _currentUser.Id;
    }
}
```
