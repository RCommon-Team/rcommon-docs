---
description: Logging behavior in the MediatR pipeline within RCommon.
---

# Logging Behavior

The [logging behavior](https://github.com/RCommon-Team/RCommon/blob/master/Src/RCommon.ApplicationServices/Behaviors/LoggingBehavior.cs) is useful any time you want to log an incoming request in the MediatR pipeline. It can be added by doing this in your dependency injection section:

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .AddLoggingToMediatorPipeline());
}</code></pre>
