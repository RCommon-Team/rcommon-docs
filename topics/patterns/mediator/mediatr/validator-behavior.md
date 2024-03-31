---
description: Validator Behavior in MediatR pipeline within RCommon.
---

# Validator Behavior

The validator behavior is useful anytime you want to de-couple request/DTO validation from your application logic. It can be added by doing this in your dependency injection section:

<pre class="language-csharp"><code class="lang-csharp"><strong>protected void InitializeRCommon(IServiceCollection services)
</strong>{

    ConfigureRCommon.Using(new DotNetCoreContainerAdapter(Services))
        .AddValidationToMediatorPipeline());
        
    // This will inject all fluent validators
    services.AddValidatorsFromAssemblyContaining&#x3C;ClassInAssemblyThatHasValidators>();
}
</code></pre>
