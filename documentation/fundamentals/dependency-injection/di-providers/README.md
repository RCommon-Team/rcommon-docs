---
description: A list of supported dependency injection containers in the RCommon framework.
---

# DI Providers

The following are a list of officially supported DI containers (providers) which we've created adapters for so that DI registration may be managed in an abstract manner.&#x20;

|                                           Provider                                           |                   Adapter                   |
| :------------------------------------------------------------------------------------------: | :-----------------------------------------: |
|                                [Autofac](https://autofac.org/)                               |    [AutofacContainerAdapter](autofac.md)    |
|                [CastleWindsor](http://www.castleproject.org/projects/windsor/)               | [WindsorContainerAdapter](castlewindsor.md) |
| [Microsoft DI](https://docs.microsoft.com/en-us/dotnet/core/extensions/dependency-injection) |  [DotNetCoreContainerAdapter](microsoft.md) |
