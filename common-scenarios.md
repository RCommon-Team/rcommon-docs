---
description: Use Cases and common scenarios for RCommon in the Enterprise
---

# Common Scenarios

Opinions run rampant in organizations when it comes to using various tooling, architectural stylings, and ultimately why or when should use this versus that. You will likely encounter many developers with many different backgrounds, and depth of architectural and operational experience. We frankly have no motive for getting you to use any particular tool. But we believe in well-built software that can expedite software delivery in your organization. So here are some common use cases to help you advocate for the same.



<details>

<summary>We are a start-up and need well-designed code but we need to move quickly. How can RCommon help?</summary>

RCommon was born from this exact scenario. The easiest thing to do to see if RCommon will work for you is to check out our [architecture ](topics/architecture/)and [examples](broken-reference).&#x20;

</details>

<details>

<summary>My architect/manager/technical lead says that we want to stay "light-weight" and simple. "We can always make it better later". How do I address that?</summary>

Our suggestion is that you ask this person some qualifying questions.&#x20;

1. Do you mind if I build a prototype application really fast?
2. Do you mind if I use commonly used design patterns and tools that will allow me to go really fast?
3. Do you mind if I use open-source code under Apache 2.0 license to accomplish all this?

If the answer to any of these questions is "no" then please reconsider who you work for. An experienced manager or engineer should always say yes to these questions. Unless you are in academics, you are building software to create value for a business. That value should be delivered quickly while not being reckless. RCommon is inherently "light-weight" and adheres to very highly scrutinized/validated patterns and practices. Entire books are written about the patterns that are implemented by RCommon - we're not reinventing the wheel here. To that end, it will prevent you from being reckless. So now we just need to go fast - check out our [architecture ](topics/architecture/)and [examples](broken-reference) to get you going.&#x20;

</details>

<details>

<summary>We already have a pre-defined architecture and tooling but I'd like to use RCommon to accomplish some future proofing and simplify some things. How do I convince my team to try it?</summary>

This can be tricky and perhaps you don't even need RCommon. RCommon is highly adaptive and can be retrofit to just about any existing code. The key is finding a simple place to start. We have a lot of examples and code that performs fundamental behaviors in any application. Create a proof of concept around one of them and then show how easy it could be to take on one of the more complex patterns later.

</details>

<details>

<summary>My architect/manager/technical lead wants to keep the codebase simple, and deal with as few dependencies as possible. Not much room for RCommon right?</summary>

Without understanding a lot about the drivers of your project, this can be a requirement of some projects that would disqualify RCommon along with many other infrastructure libraries. In this case, you may just want to copy some of our code into your project to eliminate the dependencies. You'll still have simple well-built code, save a ton of time and have full control over dependencies. But you'll lose the value of having a codebase that is maintained by the community.&#x20;

</details>

<details>

<summary>My team is indifferent to using architectural styles such as DDD, event driven applications, or ORMs. They also think layers are the devil. How do I overcome that?</summary>

RCommon is not prescriptive of any particular architectural style. Rather, it is the anti-style (not to be confused with anti-pattern). Pick whatever style you want and only use RCommon for what you need it for. While your team may think layers or whatever the en vogue programming technique is the devil, it would be hard to debate that SOLID design is a bad idea and that is where RCommon shines. How many layers you use, and what you call things is up to you.

</details>

<details>

<summary>My team embraces functional programming styles. Can RCommon help us?</summary>

There is not a technical reason that you cannot use RCommon in a functional programming language like F#. We do utilize a fluent style of coding, but we are object oriented by design.&#x20;

</details>
