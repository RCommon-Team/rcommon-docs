---
description: The ubiquitous repository pattern as it is implemented by RCommon.
---

# Repository Pattern

There has been much said about the repository pattern over the years. Perhaps the most influential [advocate for the repository pattern is Martin Fowler](https://www.martinfowler.com/eaaCatalog/repository.html).&#x20;

Through the repository pattern, we are able to a variety of other patterns that are commonly used in enterprise applications. Such patterns include [specifications](specification-pattern.md), eager loading, pagination, and [unit of work (UoW)](unit-of-work.md).&#x20;

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
