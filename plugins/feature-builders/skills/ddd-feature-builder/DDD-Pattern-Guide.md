# Domain-Driven Design (DDD) Pattern Guide

## Table of Contents
1. [What is Domain-Driven Design?](#what-is-domain-driven-design)
2. [Core Concepts](#core-concepts)
3. [Layered Architecture](#layered-architecture)
4. [Building Blocks](#building-blocks)
5. [Dependency Rules](#dependency-rules)
6. [Practical Examples](#practical-examples)
7. [Best Practices](#best-practices)
8. [Common Pitfalls](#common-pitfalls)

---

## What is Domain-Driven Design?

**Domain-Driven Design (DDD)** is a software development approach that focuses on modeling software to match the business domain as closely as possible. It emphasizes collaboration between technical and domain experts to create a shared understanding of the problem space.

### Core Philosophy

- **Business logic is king** - The domain (business rules) is the core of your application
- **Separation of concerns** - Each layer has a specific responsibility
- **Dependency inversion** - High-level modules don't depend on low-level modules; both depend on abstractions
- **Ubiquitous language** - Developers and domain experts use the same terminology

### Why Use DDD?

✅ **Maintainability** - Clear separation makes changes easier
✅ **Testability** - Domain logic can be tested without databases or external dependencies
✅ **Scalability** - Well-defined boundaries make scaling easier
✅ **Team collaboration** - Everyone speaks the same language
✅ **Flexibility** - Easy to swap implementations (database, APIs, etc.)

---

## Core Concepts

### 1. Domain
The **problem space** - the business you're trying to model. For Hammond Pole, this includes:
- Users and hierarchy
- Departments
- Document tracking
- Permissions
- POPIA compliance

### 2. Ubiquitous Language
A **shared vocabulary** between developers and business experts:
- ❌ "Parent ID" → ✅ "Reports To"
- ❌ "Active flag" → ✅ "User Status"
- ❌ "Type code" → ✅ "Application User Type"

### 3. Bounded Context
A **boundary** around a specific domain model. Example:
- **User Management Context** - Users, Roles, Permissions
- **Document Tracking Context** - Documents, Storage, POPIA
- Each context has its own model of "User" if needed

---

## Layered Architecture

DDD uses a layered architecture where dependencies flow **inward** toward the domain.

```
┌─────────────────────────────────────────┐
│           Presentation Layer            │
│         (API, UI, Controllers)          │
│   Depends on: Application, Contracts    │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│          Application Layer              │
│    (Services, Use Cases, Workflows)     │
│    Depends on: Domain, Contracts        │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│            Domain Layer                 │
│  (Entities, Value Objects, Interfaces)  │
│         Depends on: NOTHING!            │
└─────────────────────────────────────────┘
                    ↑
┌─────────────────────────────────────────┐
│         Infrastructure Layer            │
│  (Database, External APIs, Concrete)    │
│         Depends on: Domain              │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│          Contracts Layer                │
│      (DTOs, Requests, Responses)        │
│         Depends on: NOTHING!            │
└─────────────────────────────────────────┘
```

### Layer Responsibilities

| Layer | Responsibility | Examples |
|-------|---------------|----------|
| **Domain** | Business logic, rules, and entities | `ApplicationUser`, `IsValidHierarchy()`, `IUserRepository` interface |
| **Application** | Orchestration, use cases, workflows | `UserService`, `CreateUserAsync()`, DTO transformations |
| **Infrastructure** | Technical implementations | `UserRepository`, `ApplicationDbContext`, EF Core |
| **API/Presentation** | Entry points, HTTP concerns | `UserEndpoints`, routing, authentication |
| **Contracts** | Data transfer structures | `ApplicationUserDto`, `CreateUserRequest` |

---

## Building Blocks

### 1. Entities

**Objects with identity** - two entities with the same data but different IDs are different.

```csharp
// Domain Entity - Has identity (Id) and business logic
public class ApplicationUser : IdentityUser<Guid>, IAggregateRoot
{
    public Guid Id { get; set; }  // ← Identity
    public string? FirstName { get; set; }
    public string? LastName { get; set; }
    public Guid? ParentUserId { get; set; }

    // Business logic lives in the entity
    public bool IsValidHierarchy()
    {
        // Prevent self-referencing
        return !(ParentUserId.HasValue && ParentUserId.Value == Id);
    }

    public bool IsValidForDeletion()
    {
        // Cannot delete user if they have children
        return Children == null || Children.Count == 0;
    }

    // Factory method for creation
    public static ApplicationUser CreateFromAzure(
        Guid azureObjectId,
        string displayName,
        string email,
        string userName)
    {
        // Validation and business rules here
        if (azureObjectId == Guid.Empty)
            throw new DomainException("Azure object ID cannot be empty");

        return new ApplicationUser { /* ... */ };
    }
}
```

**Key Characteristics:**
- Has a unique identifier (Id)
- Contains business logic methods
- Can raise domain events
- Validates its own state

### 2. Value Objects

**Objects without identity** - defined only by their values. Two value objects with the same values are identical.

```csharp
// Value Object - No identity, immutable
public class Email
{
    public string Value { get; }

    private Email(string value)
    {
        Value = value;
    }

    public static Email Create(string email)
    {
        if (string.IsNullOrWhiteSpace(email))
            throw new DomainException("Email cannot be empty");

        if (!email.Contains('@'))
            throw new DomainException("Invalid email format");

        return new Email(email.ToLowerInvariant());
    }

    // Value objects are compared by value, not reference
    public override bool Equals(object obj)
    {
        if (obj is Email other)
            return Value == other.Value;
        return false;
    }
}

// Usage in Entity:
public class ApplicationUser
{
    private Email _userEmail;
    public Email UserEmail
    {
        get => _userEmail;
        private set => _userEmail = value;
    }

    public void UpdateEmail(Email email)
    {
        UserEmail = email;
        Email = email.Value; // Update Identity.Email too
    }
}
```

**Key Characteristics:**
- Immutable (cannot be changed after creation)
- No identity (no Id property)
- Validated at creation
- Compared by value equality

### 3. Aggregates and Aggregate Roots

**Aggregate** - A cluster of entities and value objects treated as a single unit for data changes.

**Aggregate Root** - The main entity through which all other entities in the aggregate are accessed.

```csharp
// ApplicationUser is an Aggregate Root
public class ApplicationUser : IdentityUser<Guid>, IAggregateRoot
{
    // Root entity properties
    public Guid Id { get; set; }

    // Collection of child entities (part of the aggregate)
    public virtual ICollection<ApplicationUser> Children { get; set; }

    // Navigation to related aggregate (NOT part of this aggregate)
    public virtual Department Department { get; set; }  // ← Different aggregate

    // You can ONLY modify children through the root
    public void AddChild(ApplicationUser child)
    {
        if (child.ParentUserId != this.Id)
            throw new DomainException("Child must reference this parent");

        Children.Add(child);
    }
}
```

**Aggregate Rules:**
- External objects can only reference the aggregate root
- Invariants within the aggregate are always maintained
- Database transactions should not span multiple aggregates

**Example Aggregates in Hammond Pole:**
- **User Aggregate** - Root: ApplicationUser, Children: Direct reports (same entity)
- **Department Aggregate** - Root: Department, Children: LetterheadTemplates
- **Document Aggregate** - Root: DocumentFile, Children: DestructionCertificates

### 4. Repositories

**Abstraction for accessing aggregates** - Think of it as a collection in memory.

```csharp
// Repository INTERFACE - Lives in Domain
namespace HammondPoleInc.Domain.Interfaces.Repositories;

public interface IUserRepository
{
    // Repositories work with aggregate roots only
    Task<ApplicationUser?> GetByIdAsync(Guid id, CancellationToken ct = default);
    Task<IEnumerable<ApplicationUser>> GetAllAsync(CancellationToken ct = default);
    Task AddAsync(ApplicationUser user, CancellationToken ct = default);
    Task UpdateAsync(ApplicationUser user, CancellationToken ct = default);
    Task DeleteAsync(ApplicationUser user, CancellationToken ct = default);
    Task<int> SaveChangesAsync(CancellationToken ct = default);

    // Query methods specific to the domain
    Task<IEnumerable<ApplicationUser>> GetChildrenAsync(Guid parentId, CancellationToken ct = default);
    Task<IEnumerable<ApplicationUser>> GetRootUsersAsync(CancellationToken ct = default);
}

// Repository IMPLEMENTATION - Lives in Infrastructure
namespace HammondPoleInc.Infrastructure.Repositories;

public class UserRepository : IUserRepository
{
    private readonly ApplicationDbContext _context;

    public async Task<ApplicationUser?> GetByIdAsync(Guid id, CancellationToken ct = default)
    {
        // Actual EF Core implementation
        return await _context.ApplicationUsers
            .FirstOrDefaultAsync(u => u.Id == id, ct);
    }

    // ... other implementations
}
```

**Key Characteristics:**
- Interface in Domain, implementation in Infrastructure
- Works with aggregate roots, not individual entities
- Provides domain-specific query methods
- Abstracts persistence mechanism

### 5. Domain Services

**Business logic that doesn't naturally fit in an entity** - operates on multiple entities or doesn't belong to one specific entity.

```csharp
// Domain Service - Lives in Domain layer
namespace HammondPoleInc.Domain.Services;

public interface IUserHierarchyValidator
{
    Task<bool> IsValidHierarchyAsync(Guid userId, Guid? parentId);
}

public class UserHierarchyValidator : IUserHierarchyValidator
{
    private readonly IUserRepository _userRepository;

    public async Task<bool> IsValidHierarchyAsync(Guid userId, Guid? parentId)
    {
        if (!parentId.HasValue) return true;
        if (parentId.Value == userId) return false; // Self-reference

        // Check for circular reference - needs repository to walk chain
        var parentChain = await _userRepository.GetParentChainAsync(parentId.Value);
        return !parentChain.Any(p => p.Id == userId);
    }
}
```

**When to use Domain Services:**
- Operation involves multiple aggregates
- Business logic doesn't naturally belong to one entity
- Requires repository access for validation

### 6. Application Services

**Orchestration layer** - coordinates domain objects to fulfill use cases.

```csharp
// Application Service - Lives in Application layer
namespace HammondPoleInc.Application.Services;

public class UserService : IUserService
{
    private readonly IUserRepository _userRepository;
    private readonly IUserTypeRepository _userTypeRepository;
    private readonly IDepartmentRepository _departmentRepository;
    private readonly ILogger<UserService> _logger;

    public async Task<ApplicationUserDto?> UpdateUserAsync(
        Guid id,
        UpdateApplicationUserRequest request,
        CancellationToken ct = default)
    {
        // 1. Get entity from repository
        var user = await _userRepository.GetByIdAsync(id, ct);
        if (user == null) return null;

        // 2. Business rule validation (orchestration)
        if (!string.IsNullOrEmpty(request.Email) &&
            request.Email != user.Email &&
            await _userRepository.EmailExistsAsync(request.Email, id, ct))
        {
            throw new BusinessException($"Email '{request.Email}' already exists");
        }

        // 3. Validate department exists and is active
        if (request.DepartmentId.HasValue)
        {
            var dept = await _departmentRepository.GetByIdAsync(request.DepartmentId.Value);
            if (dept == null || dept.Status != DepartmentStatus.Active)
                throw new BusinessException("Invalid or inactive department");
        }

        // 4. Use domain entity methods
        if (request.DepartmentId.HasValue)
            user.AssignDepartment(request.DepartmentId.Value, DateTime.UtcNow);

        // 5. Domain validation
        if (!user.IsValidUserData())
            throw new BusinessException("Invalid user data");

        // 6. Save changes
        await _userRepository.UpdateAsync(user, ct);
        await _userRepository.SaveChangesAsync(ct);

        // 7. Transform to DTO and return
        return TransformToDto(user);
    }

    private ApplicationUserDto TransformToDto(ApplicationUser user)
    {
        // Map domain entity to DTO
        return new ApplicationUserDto { /* ... */ };
    }
}
```

**Application Service Responsibilities:**
- Coordinate multiple repositories
- Orchestrate domain logic
- Handle transactions
- Transform domain entities to DTOs
- Validate business rules that span multiple aggregates

### 7. Domain Events

**Things that happened in the domain** - used for decoupling and side effects.

```csharp
// Domain Event - Lives in Domain layer
namespace HammondPoleInc.Domain.Events.Users;

public record UserCreatedEvent(
    Guid UserId,
    Guid? AzureGuid,
    string DisplayName,
    string Email,
    int? UserTypeId,
    DateTime CreatedAt
) : DomainEvent;

// Entity raises events
public class ApplicationUser
{
    private List<DomainEvent> _domainEvents = new();
    public IReadOnlyCollection<DomainEvent> DomainEvents => _domainEvents.AsReadOnly();

    public static ApplicationUser Create(string displayName, string email, string userName)
    {
        var user = new ApplicationUser { /* ... */ };

        // Raise domain event
        user.AddDomainEvent(new UserCreatedEvent(
            user.Id, null, user.DisplayName, user.Email, null, DateTime.UtcNow
        ));

        return user;
    }

    private void AddDomainEvent(DomainEvent eventItem)
    {
        _domainEvents.Add(eventItem);
    }
}

// Event Handler - Lives in Application layer
public class UserCreatedEventHandler : INotificationHandler<UserCreatedEvent>
{
    private readonly IEmailService _emailService;

    public async Task Handle(UserCreatedEvent notification, CancellationToken ct)
    {
        // Send welcome email
        await _emailService.SendWelcomeEmailAsync(
            notification.Email,
            notification.DisplayName
        );
    }
}
```

**Benefits of Domain Events:**
- Decoupling - handlers don't need to be called explicitly
- Audit trail - track what happened in the domain
- Side effects - trigger actions without coupling

---

## Dependency Rules

### The Golden Rule: Dependencies Point Inward

```
API/Presentation ──→ Application ──→ Domain ←── Infrastructure
       ↓                  ↓              ↑
       └────→ Contracts ←─┘              │
              (No dependencies)          │
                                         │
                   Service Defaults ─────┘
                   (Wires everything up)
```

### Valid Dependencies:

✅ **API → Application** (calls services)
✅ **API → Contracts** (uses DTOs)
✅ **Application → Domain** (uses entities, interfaces)
✅ **Application → Contracts** (transforms to DTOs)
✅ **Infrastructure → Domain** (implements interfaces)

### INVALID Dependencies:

❌ **Domain → Application** (domain can't know about app layer)
❌ **Domain → Infrastructure** (domain can't know about databases)
❌ **Domain → Contracts** (domain works with entities, not DTOs)
❌ **Application → Infrastructure** (app uses interfaces, not implementations)
❌ **Contracts → Anything** (contracts are pure data structures)

---

## Practical Examples

### Example 1: Creating a User

**1. API Layer (Endpoint)**
```csharp
// UserEndpoints.cs
private static async Task<IResult> CreateUser(
    [FromBody] CreateApplicationUserRequest request,
    [FromServices] IUserService userService)
{
    var result = await userService.CreateUserAsync(request);
    return Results.Ok(new SuccessResponse<UserCreationResponse>(result));
}
```

**2. Application Layer (Service)**
```csharp
// UserService.cs
public async Task<UserCreationResponse> CreateUserAsync(
    CreateApplicationUserRequest request,
    CancellationToken ct = default)
{
    // Business rule: Check for duplicate email
    if (await _userRepository.EmailExistsAsync(request.Email, ct))
    {
        return new UserCreationResponse
        {
            WasCreated = false,
            Message = "Email already exists"
        };
    }

    // Business rule: Validate user type exists
    if (request.ApplicationUserTypeId.HasValue)
    {
        var typeExists = await _userTypeRepository.ExistsAsync(
            request.ApplicationUserTypeId.Value, ct);
        if (!typeExists)
            return new UserCreationResponse { WasCreated = false, Message = "Invalid user type" };
    }

    // Create domain entity using factory method
    var user = ApplicationUser.Create(
        request.DisplayName,
        request.Email,
        request.UserName,
        request.ApplicationUserTypeId
    );

    // Domain validation
    if (!user.IsValidUserData())
        return new UserCreationResponse { WasCreated = false, Message = "Invalid user data" };

    // Save via repository
    await _userRepository.AddAsync(user, ct);
    await _userRepository.SaveChangesAsync(ct);

    return new UserCreationResponse
    {
        UserId = user.Id,
        WasCreated = true,
        Message = "User created successfully"
    };
}
```

**3. Domain Layer (Entity)**
```csharp
// ApplicationUser.cs
public static ApplicationUser Create(
    string displayName,
    string email,
    string userName,
    int? userTypeId = null)
{
    // Domain validation
    if (string.IsNullOrWhiteSpace(displayName))
        throw new DomainException("Display name is required");

    if (string.IsNullOrWhiteSpace(userName))
        throw new DomainException("Username is required");

    var emailVO = Email.Create(email); // Value object validation

    var user = new ApplicationUser
    {
        Id = Guid.NewGuid(),
        DisplayName = displayName.Trim(),
        UserName = userName.Trim(),
        Email = emailVO.Value,
        ApplicationUserTypeId = userTypeId,
        SyncStatus = UserSyncStatus.ReadyToSync
    };

    // Raise domain event
    user.AddDomainEvent(new UserCreatedEvent(/* ... */));

    return user;
}

public bool IsValidUserData()
{
    return !string.IsNullOrWhiteSpace(UserName) &&
           IsValidEmailFormat() &&
           IsValidHierarchy();
}
```

**4. Infrastructure Layer (Repository)**
```csharp
// UserRepository.cs
public async Task AddAsync(ApplicationUser user, CancellationToken ct = default)
{
    await _context.ApplicationUsers.AddAsync(user, ct);
}

public async Task<int> SaveChangesAsync(CancellationToken ct = default)
{
    // Dispatch domain events before saving
    var events = _context.ChangeTracker
        .Entries<IAggregateRoot>()
        .SelectMany(e => e.Entity.DomainEvents)
        .ToList();

    var result = await _context.SaveChangesAsync(ct);

    // Publish events after successful save
    foreach (var domainEvent in events)
        await _mediator.Publish(domainEvent, ct);

    return result;
}
```

### Example 2: Querying Users with Pagination

**Flow:** API → Application → Domain (interface) ← Infrastructure (implementation)

```csharp
// 1. API calls Application Service
var users = await _userService.GetPaginatedUsersAsync(query);

// 2. Application Service orchestrates
public async Task<PagedResult<ApplicationUserDto>> GetPaginatedUsersAsync(
    UserPaginationQuery query, CancellationToken ct = default)
{
    // Use repository interface (from Domain)
    var allUsers = await _userRepository.GetAllWithDetailsAsync(
        query.IncludeInactive, ct);

    // Apply business logic (filtering, searching)
    var filtered = allUsers.Where(u => /* filter criteria */);

    // Transform to DTOs
    var dtos = filtered.Select(TransformToDto).ToList();

    return new PagedResult<ApplicationUserDto> { Items = dtos, /* ... */ };
}

// 3. Infrastructure provides implementation
public async Task<IEnumerable<ApplicationUser>> GetAllWithDetailsAsync(
    bool includeInactive, CancellationToken ct = default)
{
    var query = _context.ApplicationUsers
        .Include(u => u.ApplicationUserType)
        .Include(u => u.ApplicationUserRole)
        .Include(u => u.Department)
        .AsQueryable();

    if (!includeInactive)
        query = query.Where(u => !u.LockoutEnabled || u.LockoutEnd == null);

    return await query.ToListAsync(ct);
}
```

---

## Best Practices

### 1. Keep Domain Pure

✅ **DO:**
- Put business logic in domain entities
- Use factory methods for complex creation
- Validate invariants in the entity
- Raise domain events for important state changes

❌ **DON'T:**
- Reference infrastructure concerns (DbContext, HttpClient)
- Put persistence logic in entities
- Use DTOs in domain layer
- Couple to frameworks (except Identity if needed)

### 2. Use Value Objects for Validation

✅ **DO:**
```csharp
public class Email
{
    public string Value { get; }

    private Email(string value) => Value = value;

    public static Email Create(string email)
    {
        if (string.IsNullOrWhiteSpace(email))
            throw new DomainException("Email required");
        if (!email.Contains('@'))
            throw new DomainException("Invalid email");
        return new Email(email.ToLowerInvariant());
    }
}

// Usage: var email = Email.Create(userInput); // Validated!
```

❌ **DON'T:**
```csharp
// Validation scattered everywhere
if (!email.Contains('@')) throw new Exception("Invalid");
```

### 3. Repository Pattern Guidelines

✅ **DO:**
- One repository per aggregate root
- Return domain entities, not DTOs
- Include domain-specific query methods
- Keep interfaces in Domain layer

❌ **DON'T:**
- Create repositories for every entity (only aggregate roots)
- Expose IQueryable from repository
- Put business logic in repositories
- Have repositories depend on each other

### 4. Application Service Guidelines

✅ **DO:**
- Orchestrate multiple repositories
- Handle transactions
- Transform entities to DTOs
- Validate cross-aggregate business rules

❌ **DON'T:**
- Put business logic here (belongs in domain)
- Return domain entities (use DTOs)
- Directly access DbContext (use repositories)
- Handle HTTP concerns (belongs in API)

### 5. Naming Conventions

| Concept | Naming Pattern | Example |
|---------|---------------|---------|
| Entity | Noun | `ApplicationUser`, `Department` |
| Value Object | Noun | `Email`, `Address` |
| Repository Interface | `I{Entity}Repository` | `IUserRepository` |
| Application Service | `{Entity}Service` | `UserService` |
| Domain Service | Verb/Action | `UserHierarchyValidator` |
| DTO | `{Entity}Dto` | `ApplicationUserDto` |
| Request DTO | `{Action}{Entity}Request` | `CreateApplicationUserRequest` |
| Domain Event | `{Entity}{PastTense}Event` | `UserCreatedEvent` |

### 6. Transaction Boundaries

✅ **DO:**
- One transaction per aggregate modification
- Handle transactions in Application layer
- Save all changes in a single SaveChanges call

❌ **DON'T:**
- Span transactions across multiple aggregates
- Commit transactions in Domain layer
- Have nested transactions

---

## Common Pitfalls

### 1. Anemic Domain Model

❌ **WRONG:**
```csharp
// Entity is just a bag of properties
public class ApplicationUser
{
    public Guid Id { get; set; }
    public string Email { get; set; }
    public Guid? ParentUserId { get; set; }
    // No behavior!
}

// All logic in service
public class UserService
{
    public void AssignParent(Guid userId, Guid parentId)
    {
        var user = _repo.GetById(userId);
        if (parentId == userId) throw new Exception("Self-reference!");
        user.ParentUserId = parentId; // Just setting properties
    }
}
```

✅ **CORRECT:**
```csharp
// Rich domain model with behavior
public class ApplicationUser
{
    public Guid Id { get; set; }
    public string Email { get; set; }
    public Guid? ParentUserId { get; set; }

    // Business logic in entity
    public void AssignParent(Guid? parentId, Guid assignedBy)
    {
        if (parentId.HasValue && parentId.Value == Id)
            throw new DomainException("User cannot be their own parent");

        ParentUserId = parentId;
        AddDomainEvent(new UserParentAssignedEvent(Id, parentId, assignedBy));
    }
}

// Service orchestrates
public class UserService
{
    public async Task AssignParentAsync(Guid userId, Guid parentId)
    {
        var user = await _repo.GetByIdAsync(userId);
        user.AssignParent(parentId, _currentUser.Id); // Entity enforces rules
        await _repo.SaveChangesAsync();
    }
}
```

### 2. Leaking Domain Entities to API

❌ **WRONG:**
```csharp
// API returns domain entity directly
public async Task<ApplicationUser> GetUser(Guid id)
{
    return await _userRepository.GetByIdAsync(id); // Exposing domain!
}
```

✅ **CORRECT:**
```csharp
// API returns DTO
public async Task<ApplicationUserDto> GetUser(Guid id)
{
    var user = await _userRepository.GetByIdAsync(id);
    return TransformToDto(user); // Transform to DTO
}
```

### 3. Business Logic in Infrastructure

❌ **WRONG:**
```csharp
public class UserRepository
{
    public async Task<User> GetActiveUsersAsync()
    {
        // Business rule in infrastructure!
        return await _context.Users
            .Where(u => u.Status == "Active" && u.ExpiryDate > DateTime.Now)
            .ToListAsync();
    }
}
```

✅ **CORRECT:**
```csharp
// Domain defines "active"
public class ApplicationUser
{
    public bool IsActive => !LockoutEnabled &&
                           (LockoutEnd == null || LockoutEnd < DateTimeOffset.UtcNow);
}

// Repository just fetches
public class UserRepository
{
    public async Task<IEnumerable<ApplicationUser>> GetAllAsync()
    {
        return await _context.ApplicationUsers.ToListAsync();
    }
}

// Application filters using domain logic
public class UserService
{
    public async Task<IEnumerable<ApplicationUserDto>> GetActiveUsersAsync()
    {
        var allUsers = await _userRepository.GetAllAsync();
        var activeUsers = allUsers.Where(u => u.IsActive); // Domain logic
        return activeUsers.Select(TransformToDto);
    }
}
```

### 4. Violating Aggregate Boundaries

❌ **WRONG:**
```csharp
// Modifying child directly
var child = await _userRepository.GetByIdAsync(childId);
child.ParentUserId = newParentId;
await _userRepository.SaveChangesAsync();
```

✅ **CORRECT:**
```csharp
// Modify through aggregate root
var parent = await _userRepository.GetByIdAsync(newParentId);
var child = await _userRepository.GetByIdAsync(childId);
child.AssignParent(parent.Id, _currentUser.Id); // Through domain method
await _userRepository.SaveChangesAsync();
```

---

## Summary

### DDD in One Page

**Core Principle:** Business logic lives in the domain, everything else supports it.

**Layers:**
- **Domain** - Pure business logic (entities, value objects, interfaces)
- **Application** - Orchestration and workflows
- **Infrastructure** - Technical implementations (database, APIs)
- **API** - Entry points
- **Contracts** - Data structures

**Key Patterns:**
- **Entity** - Object with identity
- **Value Object** - Object without identity, immutable
- **Aggregate** - Cluster of entities with one root
- **Repository** - Abstraction for data access
- **Domain Service** - Business logic that doesn't fit in an entity
- **Application Service** - Orchestrates domain objects

**Dependency Rule:** Dependencies flow inward toward the domain.

**Benefits:**
- ✅ Maintainable
- ✅ Testable
- ✅ Flexible
- ✅ Scalable
- ✅ Clear separation of concerns
