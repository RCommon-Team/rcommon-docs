---
description: Guid generators in RCommon
---

# GUID Generation

The use of Guids in application is ubiquitous in most modern applications. In persistence, Guids are neccesary for building enterprise applications at scale to more easily allow for sharding. In general, there are a more globally unique identifier that can be used in a large variety of circumstances.&#x20;



### Sequential Guid Generator

Sequential Guid's offer the implementor a means of tracking where a guid may have been generated from. In clustered applications, your objects may get persisted into a common data store where you can then infer which node may be persisting that information based on the initial sequence in your Guid. Not that they are a good replacement for logging, but using sequential Guid's helps engineers with identifying patterns based on where the Guid's may be generated from.

#### Configuration

```csharp
// Configuration
builder.Services.AddRCommon()
    .WithSequentialGuidGenerator(guid => 
        guid.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString);
```

#### Implementation

```csharp
 public class CreateLighthouseProfileCommandHandler : IAppRequestHandler<CreateLighthouseProfileCommand, LighthouseProfileResponse>
 {
     private readonly IGuidGenerator _guidGenerator;

     public CreateLighthouseProfileCommandHandler(IGuidGenerator guidGenerator)
     {
         _guidGenerator = guidGenerator;
     }
     public async Task<LighthouseProfileResponse> HandleAsync(CreateLighthouseProfileCommand request, CancellationToken cancellationToken)
     {
         LighthouseProfile profile = new LighthouseProfile(_guidGenerator.Create(), request.WebsiteUrl, request.CreatedByEmail);
```



### Simple Guid Generator

A simple means of generating a Guid synonomous with running Guid.NewGuid()

#### Configuration

```csharp
// Configuration
builder.Services.AddRCommon()
    .WithSimpleGuidGenerator();
```

#### Implementation

```csharp
public class CreateLighthouseProfileCommandHandler : IAppRequestHandler<CreateLighthouseProfileCommand, LighthouseProfileResponse>
 {
     private readonly IGuidGenerator _guidGenerator;

     public CreateLighthouseProfileCommandHandler(IGuidGenerator guidGenerator)
     {
         _guidGenerator = guidGenerator;
     }
     public async Task<LighthouseProfileResponse> HandleAsync(CreateLighthouseProfileCommand request, CancellationToken cancellationToken)
     {
         LighthouseProfile profile = new LighthouseProfile(_guidGenerator.Create(), request.WebsiteUrl, request.CreatedByEmail);
```
