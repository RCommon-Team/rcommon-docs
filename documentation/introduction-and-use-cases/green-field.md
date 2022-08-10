---
description: >-
  A new or relatively undefined solution architecture where you need to get
  going right away.
---

# Green Field

## Use Case

As a software engineer or application architect, you have been tasked with creating a new solution architecture for a project you have taken on. You have already chosen .NET 6+ as the foundation technology and now you are looking for an application framework that does a lot of the "plumbing" for common infrastructure components such as persistence, exception handling, unit of work, distributed events, validation, etc.&#x20;

You have some solid choices out there: [ABP Framework](https://apb.io), and [Orchard Core](https://orchardcore.net/) are some great choices depending on your needs. RCommon is yet another choice. Have a look at the comparison chart below to get a high level understanding of the differences - note this is not an exhaustive comparison:

| Feature                                                |        RCommon       |          ABP         |     Orchard Core     | Notes |
| ------------------------------------------------------ | :------------------: | :------------------: | :------------------: | ----- |
| Choice of Dependency Injection                         | :white\_check\_mark: |          :x:         |          :x:         |       |
| Loosely Coupled Architecture                           | :white\_check\_mark: | :white\_check\_mark: |   :grey\_question:   |       |
| Persistence: MongoDb                                   | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Persistence: EF Core                                   | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Persistence: Dapper                                    | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Email Sending                                          | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Validation                                             | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Granular Exception Handling                            | :white\_check\_mark: |          :x:         |          :x:         |       |
| Extensible Caching Support                             |   :grey\_question:   | :white\_check\_mark: | :white\_check\_mark: |       |
| Fluent Configuration Interface                         | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Authorization Support                                  |   :grey\_question:   | :white\_check\_mark: | :white\_check\_mark: |       |
| Background Jobs: Quartz                                | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Background Jobs: Hangfire                              | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Background Jobs: RabbitMQ                              | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Change Tracking Support                                | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Distributed Transaction Support                        | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Local/Domain Events                                    | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Message Queues: Rabbit MQ                              | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Message Queues: Azure Event Hub                        | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Message Queues: AWS SNS/SQS                            | :white\_check\_mark: |          :x:         |          :x:         |       |
| Message Queues: ActiveMQ                               | :white\_check\_mark: |          :x:         |          :x:         |       |
| Message Queues: Saga Support                           | :white\_check\_mark: |          :x:         |          :x:         |       |
| Multi-tenancy                                          | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| CLI Support                                            |          :x:         | :white\_check\_mark: |          :x:         |       |
| Workflow Management                                    |          :x:         |          :x:         | :white\_check\_mark: |       |
| DDD Support                                            | :white\_check\_mark: | :white\_check\_mark: |   :grey\_question:   |       |
| Web Application Modularity                             |          :x:         | :white\_check\_mark: | :white\_check\_mark: |       |
| User Interface Support (Razor, Blazor, Angular, React) |          :x:         | :white\_check\_mark: | :white\_check\_mark: |       |
| SignalR Support                                        | :white\_check\_mark: | :white\_check\_mark: |                      |       |
| Automatic API Generation                               |          :x:         | :white\_check\_mark: |                      |       |

