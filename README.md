---
description: >-
  RCommon is a cohesive set of libraries with abstractions for widely used
  implementations of design patterns which are common (see what we did there?)
  to many applications used today.
---

# Introduction

## Background

RCommon was created out of the need for infrastructure code which was flexible enough to be used in a wide array of enterprise applications. As the .NET ecosystems evolves, there has been a continuing need to evolve implementations/tooling in an enterprise capacity. That is to say, that while various tools get upgraded, we have a need of encapsulating that functionality to limit the impact of refactoring.

##

## What It IS

* A relatively broad cross section of software engineering patterns that have been abstracted in a way that allows a wide range of implementations/tooling to be used in a unit testing friendly manner
* Infrastructure code that can be "bootstrapped" to any .NET 6, 7, or 8 application
* Implementations of many popular libraries and patterns which cover the needs of many enterprise applications
* The glue that an application needs to allow patterns and practices to interact in a meaningful and useful way
* Battle hardened code that is being used in production across start-ups, medium sized companies and International 500 companies alike.&#x20;
* Free: Under Apache-2.0 license, you will always be able to use this code for personal or commercial purposes.



## What It IS NOT

* A framework: meaning RCommon does not invert your application. You tell us what you need, not the other way around.
* In depth implementations of each provider. RCommon covers the most common functionality of various tools but many of these tools offer a lot of depth to solve problems in their space. RCommon will not prevent you from using the tooling at an in depth level, but does not necessarily provide that level of support out of the box.&#x20;
* Ambitious: we do not plan on converting/forking RCommon into a paid license at any time

{% hint style="info" %}
**Quick Tip:** RCommon is configuration oriented which allows us to give you only what you need for your application. Before jumping into the samples, we recommend at least reading through the Fundamentals-> [Configuration ](topics/fundamentals/configuration.md)section.
{% endhint %}

### Jump Right In

Ok, we've got it...lets run some code!

{% content-ref url="getting-started/running-examples.md" %}
[running-examples.md](getting-started/running-examples.md)
{% endcontent-ref %}

### Dive a Little Deeper

{% content-ref url="topics/fundamentals/" %}
[fundamentals](topics/fundamentals/)
{% endcontent-ref %}

{% content-ref url="topics/patterns/" %}
[patterns](topics/patterns/)
{% endcontent-ref %}

{% content-ref url="topics/architecture/" %}
[architecture](topics/architecture/)
{% endcontent-ref %}



### License

You are welcome to use RCommon under dual licensing of the [Apache 2.0 software license](https://www.apache.org/licenses/LICENSE-2.0) and [MIT license](https://mit-license.org/)

{% hint style="info" %}
NOT LEGAL ADVICE: We're not lawyers but here is a little explainer about the Apache 2.0 license. The [Apache software license](https://www.apache.org/licenses/LICENSE-2.0) gives users permission to reuse code for nearly any purpose, including using the code as part of proprietary software. As with other [open source licenses](https://snyk.io/learn/open-source-licenses/), the Apache license governs how end-users can utilize the software in their own projects. The Apache license requires developers to disclose any major changes they make to the original source code. The modified source code does not need to be revealed, but a notice of the modification is required. However, any unmodified code must retain the Apache license.
{% endhint %}

### Credit

Persistence and unit of work concepts were derived from the [NCommon](https://github.com/riteshrao/ncommon) project under the Apache 2.0 license. While you may still find some of the original code from that project, we have basically redesigned everything from the ground up. That said, thanks Ritesh Rao for the inspiration way back in 2008!

CQRS, DDD, and Event Sourcing concepts would have been a lot tougher to come up with if it weren't for a lot of help from [EventFlow](https://docs.geteventflow.net/) (MIT license), [Eventuous](https://eventuous.dev/) (Apache 2.0 license), and [Revo Framework](https://docs.revoframework.net/) (MIT license). Much of their code was inspiration and some of it was downright copied - the greatest compliment of all.
