---
description: The specification pattern implementation in RCommon.
---

# Specification

Once again, [Eric Evans and Martin Fowler have much to say on the topic of specifications](https://www.martinfowler.com/apsupp/spec.pdf) so we'll leave most of the talking to them.&#x20;

The specification pattern is implemented very simply in RCommon:

```csharp
public class AllocationExistsSpec : Specification<LeaveAllocation>
{
    public AllocationExistsSpec(string userId, int leaveTypeId, int period) : 
        base(q => q.EmployeeId == userId
                && q.LeaveTypeId == leaveTypeId
                && q.Period == period)
    {
    }
}
```

Once your specification is built, it can be used with other specifications to simplify business logic in an object oriented, and reusable way. Specifications are also natively supported by RCommon repository interfaces:

```csharp
var allocationCount = await _leaveAllocationRepository
    .GetCountAsync(new AllocationExistsSpec(emp.Id, leaveType.Id, period));
```
