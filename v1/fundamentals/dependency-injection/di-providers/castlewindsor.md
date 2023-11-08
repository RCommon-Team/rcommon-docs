---
description: Windows DI container support for RCommon
---

# CastleWindsor

Windsor has created fairly comprehensive [documentation ](https://github.com/castleproject/Windsor/blob/master/docs/README.md)so we won't attempt to recompose that. Suffice to say that once you've created a type that implements IWindsorContainer, you simply pass that into the RCommon [configuration entry point ](../../configuration.md)with the WindsorContainerAdapter.&#x20;
