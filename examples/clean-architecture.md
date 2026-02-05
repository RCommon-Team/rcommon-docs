---
description: >-
  A full Clean Architecture sample application (HR Leave Management System)
  using RCommon with CQRS, MediatR, EF Core, FluentValidation, Unit of Work,
  and more.
---

# Clean Architecture

### Overview

This is the most comprehensive example in the RCommon repository - a fully functional HR Leave Management System built using Clean Architecture principles. It demonstrates how RCommon ties together multiple patterns including CQRS, Mediator, Repository, Unit of Work, FluentValidation, and more into a real-world application.

{% hint style="info" %}
This is an ASP.NET Core Web API + MVC application. You can find the full source code in the [RCommon GitHub repository](https://github.com/RCommon-Team/RCommon/tree/main/Examples/CleanWithCQRS). For setup instructions, see [Running Examples](../getting-started/running-examples.md).
{% endhint %}

### Project Structure

The solution follows Clean Architecture with these layers:

* **HR.LeaveManagement.Domain** - Domain entities and specifications
* **HR.LeaveManagement.Application** - CQRS features, DTOs, validators, and contracts
* **HR.LeaveManagement.Persistence** - EF Core DbContext and configurations
* **HR.LeaveManagement.Identity** - ASP.NET Identity and JWT authentication
* **HR.LeaveManagement.API** - ASP.NET Core Web API
* **HR.LeaveManagement.MVC** - MVC frontend

### Configuration

The API `Program.cs` demonstrates how to wire up RCommon with all its features in a real application.

```csharp
using HR.LeaveManagement.Api.Middleware;
using HR.LeaveManagement.Application;
using HR.LeaveManagement.Identity;
using HR.LeaveManagement.Persistence;
using Microsoft.Extensions.Configuration;
using RCommon;
using RCommon.Emailing.SendGrid;
using RCommon.Persistence.EFCore;
using RCommon.Security;
using Microsoft.EntityFrameworkCore;
using System.Transactions;
using RCommon.Persistence.Transactions;
using RCommon.Mediator.MediatR;
using System.Reflection;
using HR.LeaveManagement.Application.Features.LeaveAllocations.Requests.Commands;
using HR.LeaveManagement.Application.Features.LeaveAllocations.Handlers.Commands;
using HR.LeaveManagement.Application.Responses;
using HR.LeaveManagement.Application.Features.LeaveAllocations.Requests.Queries;
using HR.LeaveManagement.Application.Features.LeaveAllocations.Handlers.Queries;
using HR.LeaveManagement.Application.DTOs.LeaveAllocation;
using HR.LeaveManagement.Application.Features.LeaveRequests.Requests.Commands;
using HR.LeaveManagement.Application.Features.LeaveRequests.Handlers.Commands;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Commands;
using HR.LeaveManagement.Application.Features.LeaveTypes.Handlers.Commands;
using HR.LeaveManagement.Application.Features.LeaveRequests.Requests.Queries;
using HR.LeaveManagement.Application.Features.LeaveRequests.Handlers.Queries;
using HR.LeaveManagement.Application.DTOs.LeaveRequest;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Queries;
using HR.LeaveManagement.Application.Features.LeaveTypes.Handlers.Queries;
using HR.LeaveManagement.Application.DTOs.LeaveType;
using RCommon.ApplicationServices;
using RCommon.FluentValidation;
using Microsoft.OpenApi;
using Microsoft.Extensions.DependencyInjection;

var builder = WebApplication.CreateBuilder(args);


AddSwaggerDoc(builder.Services);
builder.Services.AddControllers();

// Add RCommon services
builder.Services.AddRCommon()
    .WithClaimsAndPrincipalAccessor()
    .WithSendGridEmailServices(x =>
    {
        var sendGridSettings = builder.Configuration.Get<SendGridEmailSettings>();
        x.SendGridApiKey = sendGridSettings.SendGridApiKey;
        x.FromNameDefault = sendGridSettings.FromNameDefault;
        x.FromEmailDefault = sendGridSettings.FromEmailDefault;
    })
    .WithDateTimeSystem(dateTime => dateTime.Kind = DateTimeKind.Utc)
    .WithSequentialGuidGenerator(guid => guid.DefaultSequentialGuidType = SequentialGuidType.SequentialAsString)
    .WithMediator<MediatRBuilder>(mediator =>
    {
        mediator.AddRequest<CreateLeaveAllocationCommand, BaseCommandResponse, CreateLeaveAllocationCommandHandler>();
        mediator.AddRequest<DeleteLeaveAllocationCommand, DeleteLeaveAllocationCommandHandler>();
        mediator.AddRequest<UpdateLeaveAllocationCommand, UpdateLeaveAllocationCommandHandler>();
        mediator.AddRequest<GetLeaveAllocationDetailRequest, LeaveAllocationDto, GetLeaveAllocationDetailRequestHandler>();
        mediator.AddRequest<GetLeaveAllocationListRequest, List<LeaveAllocationDto>, GetLeaveAllocationListRequestHandler>();
        mediator.AddRequest<CreateLeaveAllocationCommand, BaseCommandResponse, CreateLeaveAllocationCommandHandler>();
        mediator.AddRequest<DeleteLeaveAllocationCommand, DeleteLeaveAllocationCommandHandler>();
        mediator.AddRequest<UpdateLeaveAllocationCommand, UpdateLeaveAllocationCommandHandler>();
        mediator.AddRequest<CreateLeaveRequestCommand, BaseCommandResponse, CreateLeaveRequestCommandHandler>();
        mediator.AddRequest<DeleteLeaveRequestCommand, DeleteLeaveRequestCommandHandler>();
        mediator.AddRequest<UpdateLeaveRequestCommand, UpdateLeaveRequestCommandHandler>();
        mediator.AddRequest<CreateLeaveTypeCommand, BaseCommandResponse, CreateLeaveTypeCommandHandler>();
        mediator.AddRequest<GetLeaveRequestDetailRequest, LeaveRequestDto, GetLeaveRequestDetailRequestHandler>();
        mediator.AddRequest<GetLeaveRequestListRequest, List<LeaveRequestListDto>, GetLeaveRequestListRequestHandler>();
        mediator.AddRequest<CreateLeaveTypeCommand, BaseCommandResponse, CreateLeaveTypeCommandHandler>();
        mediator.AddRequest<DeleteLeaveTypeCommand, DeleteLeaveTypeCommandHandler>();
        mediator.AddRequest<UpdateLeaveTypeCommand, UpdateLeaveTypeCommandHandler>();
        mediator.AddRequest<GetLeaveTypeDetailRequest, LeaveTypeDto, GetLeaveTypeDetailRequestHandler>();
        mediator.AddRequest<GetLeaveTypeListRequest, List<LeaveTypeDto>, GetLeaveTypeListRequestHandler>();

        mediator.Configure(config =>
        {
            config.RegisterServicesFromAssemblies((typeof(ApplicationServicesRegistration).GetTypeInfo().Assembly));
        });
        mediator.AddLoggingToRequestPipeline();
        mediator.AddUnitOfWorkToRequestPipeline();
    })
    .WithPersistence<EFCorePerisistenceBuilder>(ef => // Repository/ORM configuration. We could easily swap out to NHibernate without impact to domain service up through the stack
    {
        // Add all the DbContexts here
        ef.AddDbContext<LeaveManagementDbContext>(DataStoreNamesConst.LeaveManagement, options =>
        {
            options.UseSqlServer(
                builder.Configuration.GetConnectionString(DataStoreNamesConst.LeaveManagement));
        });
        ef.SetDefaultDataStore(dataStore =>
        {
            dataStore.DefaultDataStoreName = DataStoreNamesConst.LeaveManagement;
        });
    })
    .WithUnitOfWork<DefaultUnitOfWorkBuilder>(unitOfWork =>
    {
        unitOfWork.SetOptions(options =>
        {
            options.AutoCompleteScope = true;
            options.DefaultIsolation = IsolationLevel.ReadCommitted;
        });
    })
    .WithValidation<FluentValidationBuilder>(validation =>
    {
        validation.AddValidatorsFromAssemblyContaining(typeof(ApplicationServicesRegistration));
    });

// Add services to the container.
builder.Services.ConfigureApplicationServices();
builder.Services.ConfigureIdentityServices(builder.Configuration);
builder.Services.AddHttpContextAccessor();

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddCors(o =>
{
    o.AddPolicy("CorsPolicy",
        builder => builder.AllowAnyOrigin()
        .AllowAnyMethod()
        .AllowAnyHeader());
});

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}

app.UseMiddleware<ExceptionMiddleware>();

app.UseAuthentication();
app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "HR.LeaveManagement.Api v1"));
app.UseHttpsRedirection();

app.UseAuthorization();

app.UseCors("CorsPolicy");

app.MapControllers();

app.Run();
```

### Domain Layer

#### Base Entity

All domain entities inherit from `AuditedEntity` which provides auditing fields automatically.

```csharp
using RCommon.Entities;
using System;
using System.Collections.Generic;
using System.Text;

namespace HR.LeaveManagement.Domain.Common
{
    public abstract class BaseDomainEntity : AuditedEntity<int, string, string>
    {

    }
}
```

#### Domain Entities

```csharp
using HR.LeaveManagement.Domain.Common;
using System;
using System.Collections.Generic;
using System.Text;

namespace HR.LeaveManagement.Domain
{
    public class LeaveType : BaseDomainEntity
    {

        public LeaveType()
        {

        }

        public string Name { get; set; }
        public int DefaultDays { get; set; }
    }
}
```

```csharp
using HR.LeaveManagement.Domain.Common;
using System;
using System.Collections.Generic;
using System.Text;

namespace HR.LeaveManagement.Domain
{
    public class LeaveRequest : BaseDomainEntity
    {
        public LeaveRequest()
        {

        }


        public DateTime StartDate { get; set; }
        public DateTime EndDate { get; set; }
        public LeaveType LeaveType { get; set; }
        public int LeaveTypeId { get; set; }
        public DateTime DateRequested { get; set; }
        public string RequestComments { get; set; }
        public DateTime? DateActioned { get; set; }
        public bool? Approved { get; set; }
        public bool Cancelled { get; set; }
        public string RequestingEmployeeId { get; set; }

    }
}
```

### Application Layer

#### Command

Commands implement `IAppRequest<TResponse>` from RCommon's mediator abstraction.

```csharp
using HR.LeaveManagement.Application.DTOs.LeaveType;
using HR.LeaveManagement.Application.Responses;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Text;

namespace HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Commands
{
    public class CreateLeaveTypeCommand : IAppRequest<BaseCommandResponse>
    {
        public CreateLeaveTypeDto LeaveTypeDto { get; set; }

    }
}
```

#### Command Handler

Command handlers implement `IAppRequestHandler<TRequest, TResponse>` and use RCommon's `IGraphRepository` for persistence and `IValidationService` for validation.

```csharp
using AutoMapper;
using HR.LeaveManagement.Application.DTOs.LeaveType.Validators;
using HR.LeaveManagement.Application.Exceptions;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Commands;
using HR.LeaveManagement.Domain;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using HR.LeaveManagement.Application.Responses;
using System.Linq;
using RCommon.Persistence;
using RCommon.Persistence.Crud;
using RCommon.ApplicationServices.Validation;

namespace HR.LeaveManagement.Application.Features.LeaveTypes.Handlers.Commands
{
    public class CreateLeaveTypeCommandHandler : IAppRequestHandler<CreateLeaveTypeCommand, BaseCommandResponse>
    {
        private readonly IMapper _mapper;
        private readonly IGraphRepository<LeaveType> _leaveTypeRepository;
        private readonly IValidationService _validationService;

        public CreateLeaveTypeCommandHandler(IMapper mapper, IGraphRepository<LeaveType> leaveTypeRepository, IValidationService validationService)
        {
            _mapper = mapper;
            _leaveTypeRepository = leaveTypeRepository;
            _validationService = validationService;
            this._leaveTypeRepository.DataStoreName = DataStoreNamesConst.LeaveManagement;
        }

        public async Task<BaseCommandResponse> HandleAsync(CreateLeaveTypeCommand request, CancellationToken cancellationToken)
        {
            var response = new BaseCommandResponse();
            var validationResult = await _validationService.ValidateAsync(request.LeaveTypeDto);

            if (validationResult.IsValid == false)
            {
                response.Success = false;
                response.Message = "Creation Failed";
                response.Errors = validationResult.Errors.Select(q => q.ErrorMessage).ToList();
            }
            else
            {
                var leaveType = _mapper.Map<LeaveType>(request.LeaveTypeDto);

                await _leaveTypeRepository.AddAsync(leaveType);

                response.Success = true;
                response.Message = "Creation Successful";
                response.Id = leaveType.Id;
            }

            return response;
        }
    }
}
```

#### Query and Query Handler

```csharp
using HR.LeaveManagement.Application.DTOs;
using HR.LeaveManagement.Application.DTOs.LeaveType;
using RCommon.Mediator.Subscribers;
using System;
using System.Collections.Generic;
using System.Text;

namespace HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Queries
{
    public class GetLeaveTypeListRequest : IAppRequest<List<LeaveTypeDto>>
    {
    }
}
```

```csharp
using AutoMapper;
using HR.LeaveManagement.Application.DTOs;
using HR.LeaveManagement.Application.DTOs.LeaveType;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Queries;
using HR.LeaveManagement.Domain;
using RCommon.Mediator.Subscribers;
using RCommon.Persistence;
using RCommon.Persistence.Crud;
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace HR.LeaveManagement.Application.Features.LeaveTypes.Handlers.Queries
{
    public class GetLeaveTypeListRequestHandler : IAppRequestHandler<GetLeaveTypeListRequest, List<LeaveTypeDto>>
    {
        private readonly IGraphRepository<LeaveType> _leaveTypeRepository;
        private readonly IMapper _mapper;

        public GetLeaveTypeListRequestHandler(IGraphRepository<LeaveType> leaveTypeRepository, IMapper mapper)
        {
            _leaveTypeRepository = leaveTypeRepository;
            this._leaveTypeRepository.DataStoreName = DataStoreNamesConst.LeaveManagement;
            _mapper = mapper;
        }

        public async Task<List<LeaveTypeDto>> HandleAsync(GetLeaveTypeListRequest request, CancellationToken cancellationToken)
        {
            var leaveTypes = await _leaveTypeRepository.FindAsync(x=> true);
            return _mapper.Map<List<LeaveTypeDto>>(leaveTypes);
        }
    }
}
```

### Persistence Layer

#### DbContext

The `LeaveManagementDbContext` extends `AuditableDbContext` which automatically tracks creation and modification metadata.

```csharp
using HR.LeaveManagement.Domain;
using HR.LeaveManagement.Domain.Common;
using Microsoft.EntityFrameworkCore;
using RCommon;
using RCommon.Entities;
using RCommon.Security.Users;
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace HR.LeaveManagement.Persistence
{
    public class LeaveManagementDbContext : AuditableDbContext
    {

        public LeaveManagementDbContext(DbContextOptions<LeaveManagementDbContext> options, ICurrentUser currentUser, ISystemTime systemTime)
            : base(options, currentUser, systemTime)
        {
        }

        public LeaveManagementDbContext(DbContextOptions<LeaveManagementDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.ApplyConfigurationsFromAssembly(typeof(LeaveManagementDbContext).Assembly);
        }

        public DbSet<LeaveRequest> LeaveRequests { get; set; }
        public DbSet<LeaveType> LeaveTypes { get; set; }
        public DbSet<LeaveAllocation> LeaveAllocations { get; set; }
    }
}
```

### API Layer

#### Controller

Controllers use `IMediatorService` to dispatch commands and queries, keeping the API layer thin and focused on HTTP concerns.

```csharp
using HR.LeaveManagement.Application.DTOs.LeaveType;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Commands;
using HR.LeaveManagement.Application.Features.LeaveTypes.Requests.Queries;
using HR.LeaveManagement.Application.Responses;
using MediatR;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using RCommon.Mediator;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace HR.LeaveManagement.Api.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    [Authorize]
    public class LeaveTypesController : ControllerBase
    {
        private readonly IMediatorService _mediator;
        private readonly IHttpContextAccessor _httpContextAccessor;

        public LeaveTypesController(IMediatorService mediator, IHttpContextAccessor httpContextAccessor)
        {
            _mediator = mediator;
            this._httpContextAccessor = httpContextAccessor;
        }

        // GET: api/<LeaveTypesController>
        [HttpGet]
        public async Task<ActionResult<List<LeaveTypeDto>>> Get()
        {
            var leaveTypes = await _mediator.Send< GetLeaveTypeListRequest, List<LeaveTypeDto>>(new GetLeaveTypeListRequest());
            return Ok(leaveTypes);
        }

        // GET api/<LeaveTypesController>/5
        [HttpGet("{id}")]
        public async Task<ActionResult<LeaveTypeDto>> Get(int id)
        {
            var leaveType = await _mediator.Send<GetLeaveTypeDetailRequest, LeaveTypeDto>(new GetLeaveTypeDetailRequest { Id = id });
            return Ok(leaveType);
        }

        // POST api/<LeaveTypesController>
        [HttpPost]
        [ProducesResponseType(200)]
        [ProducesResponseType(400)]
        [Authorize(Roles = "Administrator")]
        public async Task<ActionResult<BaseCommandResponse>> Post([FromBody] CreateLeaveTypeDto leaveType)
        {
            var user = _httpContextAccessor.HttpContext.User;
            var command = new CreateLeaveTypeCommand { LeaveTypeDto = leaveType };
            var response = await _mediator.Send<CreateLeaveTypeCommand, BaseCommandResponse>(command);
            return Ok(response);
        }

        // PUT api/<LeaveTypesController>
        [HttpPut("{id}")]
        [ProducesResponseType(StatusCodes.Status204NoContent)]
        [ProducesResponseType(StatusCodes.Status404NotFound)]
        [ProducesDefaultResponseType]
        [Authorize(Roles = "Administrator")]
        public async Task<ActionResult> Put([FromBody] LeaveTypeDto leaveType)
        {
            var command = new UpdateLeaveTypeCommand { LeaveTypeDto = leaveType };
            await _mediator.Send(command);
            return NoContent();
        }

        // DELETE api/<LeaveTypesController>/5
        [HttpDelete("{id}")]
        [ProducesResponseType(StatusCodes.Status204NoContent)]
        [ProducesResponseType(StatusCodes.Status404NotFound)]
        [ProducesDefaultResponseType]
        [Authorize(Roles = "Administrator")]
        public async Task<ActionResult> Delete(int id)
        {
            var command = new DeleteLeaveTypeCommand { Id = id };
            await _mediator.Send(command);
            return NoContent();
        }
    }
}
```

### Learn More

{% content-ref url="../getting-started/running-examples.md" %}
[running-examples.md](../getting-started/running-examples.md)
{% endcontent-ref %}

{% content-ref url="../topics/architecture/clean-architecture.md" %}
[clean-architecture.md](../topics/architecture/clean-architecture.md)
{% endcontent-ref %}
