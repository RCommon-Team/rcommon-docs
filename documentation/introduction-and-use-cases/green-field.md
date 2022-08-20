---
description: >-
  A new or relatively undefined solution architecture where you need to get
  going right away.
---

# Green Field

## Use Case

As a software engineer or application architect, you have been tasked with creating a new solution architecture for a project you have taken on. You have already chosen .NET 6+ as the foundation technology and now you are looking for an application framework that does a lot of the "plumbing" for common infrastructure components such as persistence, exception handling, unit of work, distributed events, validation, etc.&#x20;

You have some solid choices out there: [ABP Framework](https://apb.io), and [Orchard Core](https://orchardcore.net/) are some great choices if you are looking for all around good web application frameworks. RCommon is less of a framework and more a set of cohesive tools where you can use one of them, or all of them. Whereas application frameworks require inversion of control over your solution to some degree where you need to adopt the architecture wholesale, RCommon is focused on allowing application architects to enhance their solutions one bit at a time. Have a look at the comparison chart below to get a high level understanding of the differences - note this is not an exhaustive comparison:

| Feature                                                |        RCommon       |          ABP         |     Orchard Core     | Notes |
| ------------------------------------------------------ | :------------------: | :------------------: | :------------------: | ----- |
| Loosely Coupled Architecture                           | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Persistence: MongoDb                                   |          :x:         | :white\_check\_mark: |          :x:         |       |
| Persistence: EF Core                                   | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Persistence: Dapper                                    | :white\_check\_mark: | :white\_check\_mark: |          :x:         |       |
| Email Sending                                          | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Validation                                             | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Granular Exception Handling                            | :white\_check\_mark: |          :x:         |          :x:         |       |
| Extensible Caching Support                             |          :x:         | :white\_check\_mark: | :white\_check\_mark: |       |
| Fluent Configuration Interface                         | :white\_check\_mark: | :white\_check\_mark: | :white\_check\_mark: |       |
| Authorization Support                                  |          :x:         | :white\_check\_mark: | :white\_check\_mark: |       |
| Background Jobs: Quartz                                |          :x:         | :white\_check\_mark: |          :x:         |       |
| Background Jobs: Hangfire                              |          :x:         | :white\_check\_mark: |          :x:         |       |
| Background Jobs: RabbitMQ                              |          :x:         | :white\_check\_mark: |          :x:         |       |
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
| SignalR Support                                        |          :x:         | :white\_check\_mark: |                      |       |
| Automatic API Generation                               |          :x:         | :white\_check\_mark: |                      |       |

