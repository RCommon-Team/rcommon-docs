---
description: The ubiquitous repository pattern as it is implemented by RCommon.
---

# Repository

There has been much said about the repository pattern over the years. Perhaps the most influential [advocate for the repository pattern is Martin Fowler](https://www.martinfowler.com/eaaCatalog/repository.html).&#x20;

Through the repository pattern, we are able to implement a variety of useful techniques such as eager loading, pagination, and [unit of work (UoW)](../transactions/unit-of-work.md).&#x20;

```csharp
public class DeleteLeaveRequestCommandHandler : IRequestHandler<DeleteLeaveRequestCommand>
{
    private readonly IFullFeaturedRepository<LeaveRequest> _leaveRequestRepository;

    public DeleteLeaveRequestCommandHandler(IFullFeaturedRepository<LeaveRequest> leaveRequestRepository)
    {
        this._leaveRequestRepository = leaveRequestRepository;
        this._leaveRequestRepository.DataStoreName = "LeaveManagement";
    }

    public async Task<Unit> Handle(DeleteLeaveRequestCommand request, CancellationToken cancellationToken)
    {
        var leaveRequest = await _leaveRequestRepository.FindAsync(request.Id);

        if (leaveRequest == null)
            throw new NotFoundException(nameof(LeaveRequest), request.Id);

        await _leaveRequestRepository.DeleteAsync(leaveRequest);
        return Unit.Value;
    }
}
```

&#x20;

### Persistence Features

RCommon provides a variety of strategies for persistence. Our opinion is that object relational mappers (ORM's) can be divided into three categories with distinct but not exclusive feature sets:

<table><thead><tr><th width="269">Feature</th><th width="152" align="center">Graph Mapper</th><th width="138" align="center">Linq Mapper</th><th width="165" align="center">SQL Mapper</th></tr></thead><tbody><tr><td>Implements IQueryable</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td></tr><tr><td>Eager Loading</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td></tr><tr><td>Graph Persistence (nested entities)</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td></tr><tr><td>Change Tracking</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td></tr><tr><td>ACID Transactions</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Custom SQL/Queries</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Fluent Entity Mapping</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Persistence Events</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Unit of Work Support</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr></tbody></table>



### Supported Object Relational Mappers (ORMs)

<table><thead><tr><th width="269">ORM</th><th width="152" align="center">Graph Mapper</th><th width="138" align="center">Linq Mapper</th><th width="165" align="center">SQL Mapper</th></tr></thead><tbody><tr><td>Entity Framework Core</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Mongo DB Client (soon)</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td></tr><tr><td>Linq2Db (soon)</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Dapper</td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="274c">❌</span></td><td align="center"><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr></tbody></table>

