---
description: Time and Date Abstractions in RCommon
---

# Time and Date

Having the ability to mock date/time constructs seems long overdue. .NET 8 only recently addressed the capability gap through the [TimeProvider](https://learn.microsoft.com/en-us/dotnet/api/system.timeprovider?view=net-8.0) abstraction. RCommon has had the ISystemTime abstraction for many years now.&#x20;



### Configuration

```csharp
builder.Services.AddRCommon()
    .WithDateTimeSystem(dateTime => dateTime.Kind = DateTimeKind.Utc)
```



### Implementation

```csharp
public class CreateLighthouseProfileCommandHandler : IAppRequestHandler<CreateLighthouseProfileCommand, LighthouseProfileResponse>
 {
     private readonly ISystemTime _systemTime;

     public CreateLighthouseProfileCommandHandler(ISystemTime systemTime)
     {
         _systemTime = systemTime;
     }
     public async Task<LighthouseProfileResponse> HandleAsync(CreateLighthouseProfileCommand request, CancellationToken cancellationToken)
     {
         LighthouseProfile profile = new LighthouseProfile(_systemTime.Now, request.WebsiteUrl, request.CreatedByEmail);
```
