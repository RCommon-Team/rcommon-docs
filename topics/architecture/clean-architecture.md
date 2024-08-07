---
description: Clean Architecture using RCommon
---

# Clean Architecture

### Overview

[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) is a concept put forth by Robert C. Martin (Uncle Bob) which utilizes a rule-based layered architecture represented as concentric circles.&#x20;

The tenants of this architecture are the following:

1. Independent of Frameworks. The architecture does not depend on the existence of some library of feature laden software. This allows you to use such frameworks as tools, rather than having to cram your system into their limited constraints.
2. Testable. The business rules can be tested without the UI, Database, Web Server, or any other external element.
3. Independent of UI. The UI can change easily, without changing the rest of the system. A Web UI could be replaced with a console UI, for example, without changing the business rules.
4. Independent of Database. You can swap out Oracle or SQL Server, for Mongo, BigTable, CouchDB, or something else. Your business rules are not bound to the database.
5. Independent of any external agency. In fact your business rules simply don’t know anything at all about the outside world.

<figure><img src="../../.gitbook/assets/image (1).png" alt="Clean Architecture Diagram"><figcaption><p>Image Credit by Robert C. Martin <a href="https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html">https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html</a></p></figcaption></figure>



#### Interface Adapters Layer

Coming soon...



#### Application Business Rules

Coming soon...



#### Enterprise Business Rules Layer

Coming soon...





