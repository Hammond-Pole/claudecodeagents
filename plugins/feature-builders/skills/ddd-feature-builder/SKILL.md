# DDD Feature Builder

Build, enhance, and fix features following Domain-Driven Design patterns with full layer implementation and Test-Driven Development.

## Trigger

Use this skill when the user explicitly requests:
- **CREATE**: "Create a new feature", "Add feature [name]", "Implement [feature] following DDD"
- **ENHANCE**: "Enhance [feature]", "Add functionality to [feature]", "Extend [feature]"
- **FIX**: "Fix bug in [feature]", "Debug [feature]", "There's an issue with [feature]"

## Instructions

You are an expert DDD architect working on the Hammond Pole Operations Platform. Follow these steps exactly:

---

## PHASE -1: PROJECT ARCHITECTURE SETUP

**CRITICAL: Before starting, ensure the project has the required directory structure.**

### Step 1: Check for ProjectArchitecture Directory

Use the Bash tool to check if `ProjectArchitecture/` exists:
```bash
test -d ProjectArchitecture && echo "EXISTS" || echo "NOT_FOUND"
```

### Step 2: If NOT_FOUND, Create Directory Structure

```bash
mkdir -p ProjectArchitecture/{Plans,FinalisedBackEndReports,FinalisedFrontEndReports}
```

### Step 3: Check for DDD Pattern Guide

Use Glob to check if `ProjectArchitecture/DDD-Pattern-Guide.md` exists.

**If NOT found:**
1. Read the bundled DDD-Pattern-Guide.md from this skill's directory
2. Write it to `ProjectArchitecture/DDD-Pattern-Guide.md` in the project

**The bundled guide is located at the same level as this SKILL.md file.**

### Step 4: Inform User

Tell the user:
"✅ Project architecture structure verified. Plans will be saved to `ProjectArchitecture/Plans/` and reports to `ProjectArchitecture/FinalisedBackEndReports/`."

---

## PHASE 0: OPERATION MODE DETECTION

**First, determine what the user wants to do:**

Use the AskUserQuestion tool to ask:

**Question:** "What would you like to do?"

**Options:**
- **Create a New Feature** - Build a new aggregate/feature from scratch
- **Enhance an Existing Feature** - Add functionality to existing code
- **Fix a Bug** - Fix an issue in existing feature

Based on the answer, proceed to the appropriate discovery phase:
- CREATE → Go to PHASE 1A
- ENHANCE → Go to PHASE 1B
- FIX → Go to PHASE 1C

---

## PHASE 1A: DISCOVERY - CREATE MODE (New Feature)

Ask ALL questions upfront before any implementation. Use the AskUserQuestion tool to gather:

### Question Set 1: Feature Definition
1. **Feature Name**: What is the feature called? (e.g., "Project Management", "Invoice Tracking")
2. **Feature Description**: Brief description of what this feature does (2-3 sentences)
3. **Primary Aggregate Root**: What is the main entity? (e.g., "Project", "Invoice", "Contract")

### Question Set 2: Entity Structure
4. **Entity Properties**: List all properties with types
   - Format: "PropertyName (Type), PropertyName (Type), ..."
   - Example: "Name (string), Description (string), StartDate (DateTime), Budget (decimal), Status (enum)"
5. **Value Objects**: Any value objects needed? (e.g., "Money", "Address", "ProjectStatus")
   - If none, user can say "None"
6. **Navigation Properties**: What are the relationships?
   - Format: "RelatedEntity (relationship type)"
   - Example: "Department (many-to-one), Users (many-to-many), Tasks (one-to-many)"

### Question Set 3: Business Rules
7. **Business Rules**: List key business rules and validations
   - Example: "Budget cannot be negative", "End date must be after start date", "Cannot delete if has active tasks"
8. **Required Properties**: Which properties are required/mandatory?

### Question Set 4: Operations
9. **CRUD Operations**: Which operations are needed?
   - Options: Create, Read/GetById, List/Search with pagination, Update, Delete, Toggle Active Status
10. **Custom Operations**: Any special domain operations?
    - Example: "CompleteProject", "AssignMember", "CalculateTotalCost", "ApproveInvoice"
    - If none, user can say "None"

### Question Set 5: API Configuration
11. **Endpoint Prefix**: What should the API path be? (e.g., "/api/projects", "/api/invoices")
12. **Authorization Required**: Should endpoints require authorization? (Yes/No)
13. **Permission Name**: If authorized, what permission? (e.g., "PROJECT_MANAGE", "INVOICE_VIEW")

---

## PHASE 1B: DISCOVERY - ENHANCE MODE (Existing Feature)

Ask ALL questions upfront before any implementation. Use the AskUserQuestion tool to gather:

### Question Set 1: Feature Identification
1. **Which Feature to Enhance**: Which aggregate/feature needs enhancement?
   - Auto-detect existing aggregates in `HammondPoleInc.Domain/Entities/`
   - Present list to user for selection
   - Example: "ApplicationUser", "Department", "DocumentFile"

### Question Set 2: Scan Current Implementation

After user selects the feature, scan existing files:
- Domain entity file
- Repository interface
- Service interface and implementation
- API endpoints
- Existing tests

**Show user the current structure:**
```
Current [Entity] Implementation:
- Properties: [list current properties]
- Business Rules: [list current methods]
- Operations: [list current operations]
- Endpoints: [list current endpoints]
```

### Question Set 3: Enhancement Details
2. **What to Add**: What functionality should be added?
   - Options (multi-select):
     - [ ] New Properties
     - [ ] New Business Rules
     - [ ] New Operations/Methods
     - [ ] New API Endpoints
     - [ ] New Relationships

3. **New Properties** (if selected): List properties to add with types
   - Format: "PropertyName (Type), PropertyName (Type)"
   - Example: "Budget (decimal), DueDate (DateTime?)"

4. **New Business Rules** (if selected): Describe new validation rules
   - Example: "Budget cannot exceed department limit", "DueDate must be in future"

5. **New Operations** (if selected): List new domain operations
   - Example: "ApproveBudget", "ExtendDeadline", "AssignTeamMember"

6. **New Relationships** (if selected): Describe new relationships
   - Format: "RelatedEntity (relationship type)"
   - Example: "Budget (many-to-one)", "TeamMembers (many-to-many)"

7. **New Endpoints** (if selected): List new API endpoints needed
   - Example: "POST /api/projects/{id}/approve", "PUT /api/projects/{id}/extend-deadline"

8. **Backward Compatibility**: Are existing clients/frontends using this feature?
   - If Yes: Only non-breaking changes allowed
   - If No: Breaking changes are acceptable

### Question Set 4: Migration Requirements
9. **Database Changes**: Will this require database migration?
   - Auto-detected based on new properties/relationships
   - Confirm with user

---

## PHASE 1C: DISCOVERY - FIX MODE (Bug Fix)

Ask ALL questions upfront before any implementation. Use the AskUserQuestion tool to gather:

### Question Set 1: Bug Identification
1. **Which Feature Has Bug**: Which aggregate/feature has the bug?
   - Auto-detect existing aggregates
   - Present list to user for selection

### Question Set 2: Bug Details (Critical for Auto-Detection)
2. **Bug Description**: Describe what's happening (the bug behavior)
   - Detailed description helps identify which layer has the issue
   - Example: "When I create a user with a duplicate email, it returns 500 instead of 400"

3. **Expected Behavior**: What should happen instead?
   - Example: "Should return 400 Bad Request with message 'Email already exists'"

4. **Steps to Reproduce**: How can we reproduce the bug?
   - Example: "1. Create user with email test@example.com, 2. Create another user with same email"

5. **Error Messages/Logs**: Any error messages or stack traces?
   - Paste any error messages, stack traces, or logs
   - If none, user can say "None"

6. **When Does it Occur**: When does the bug happen?
   - Options:
     - Always (consistent bug)
     - Sometimes (intermittent)
     - Specific conditions (describe)

### Question Set 3: Context
7. **Recent Changes**: Was any code changed recently related to this?
   - Helps identify if bug is new or regression

8. **Affected Layer** (Optional): Do you know which layer has the issue?
   - If user knows: Domain / Service / API / Repository / Frontend
   - If user doesn't know: "Not sure - please detect automatically"

---

## PHASE 2: AGGREGATE DETECTION (All Modes)

Before showing the implementation plan, scan the codebase to detect existing aggregates:

1. Search for existing entities in `HammondPoleInc.Domain/Entities/`
2. Search for existing repositories in `HammondPoleInc.Domain/Interfaces/Repositories/`
3. Create a list of detected aggregates (e.g., ApplicationUser, Department, DocumentFile)
4. If user mentioned relationships to existing aggregates, validate they exist

---

## PHASE 3: IMPLEMENTATION PLAN (Mode-Specific)

**CRITICAL: Save the implementation plan to ProjectArchitecture/Plans/ for future reference!**

### Plan Document Format:

**File Name:** `ProjectArchitecture/Plans/[Mode]-[Entity]-[Timestamp].md`
- CREATE mode: `CREATE-Project-20251118-110000.md`
- ENHANCE mode: `ENHANCE-Department-20251118-110000.md`
- FIX mode: `FIX-User-DuplicateEmail-20251118-110000.md`

**Plan Structure:**

```markdown
# [Mode]: [Feature Name]

**Generated:** [Timestamp]
**Mode:** [CREATE/ENHANCE/FIX]
**Aggregate:** [Entity Name]
**Status:** Planning

---

## 1. Requirements Summary

### Discovery Answers:
[User's answers to all discovery questions formatted clearly]

Example for CREATE:
- Feature Name: Project Management
- Description: Tracks construction projects with budgets and timelines
- Primary Aggregate: Project
- Properties: Name (string), Description (string), Budget (decimal), StartDate (DateTime)
- Business Rules: Budget cannot be negative, EndDate must be after StartDate
- Operations: Create, Update, Delete, GetById, List, CompleteProject
- Endpoints: /api/projects
- Authorization: Yes (PROJECT_MANAGE permission)

---

## 2. Detected Aggregates

Existing aggregates found in codebase:
- ApplicationUser (Configuration context)
- Department (Configuration context)
- DocumentFile (Documents context)
- [... complete list]

Relationships to existing aggregates:
- [Entity] → Department (many-to-one)
- [Entity] → ApplicationUser (assigned user, many-to-one)

---

## 3. Implementation Plan

### Files to Create: [X files]

**Domain Layer:** (X files)
- `HammondPoleInc.Domain/Entities/[Context]/[Entity].cs`
- `HammondPoleInc.Domain/Interfaces/Repositories/I[Entity]Repository.cs`
- `HammondPoleInc.Domain/Events/[Context]/[Entity]CreatedEvent.cs`
- `HammondPoleInc.Domain/Events/[Context]/[Entity]UpdatedEvent.cs`
- [... complete list]

**Infrastructure Layer:** (X files)
- [... complete list]

**Application Layer:** (X files)
- [... complete list]

**Contracts Layer:** (X files)
- [... complete list]

**API Layer:** (X files)
- [... complete list]

**Tests:** (X files)
- [... complete list]

### Files to Modify: [X files]
[For ENHANCE and FIX modes]

---

## 4. TDD Strategy

### Red-Green-Refactor Plan:

**🔴 RED Phase:**
1. Write domain entity tests first → Will FAIL
2. Write E2E API tests → Will FAIL

**🟢 GREEN Phase:**
1. Implement domain entity → Tests PASS
2. Implement infrastructure, application, API → E2E tests PASS

**🔵 REFACTOR Phase:**
1. Improve code quality
2. Keep all tests GREEN

### Test Coverage Goals:
- Domain entity: 100% coverage of business rules
- E2E: All API endpoints tested
- Total: Meet project threshold (90%)

---

## 5. Backward Compatibility Analysis
[For ENHANCE mode only]

### Breaking Changes Check:
- ✅ New properties are nullable: [Yes/No]
- ✅ Existing endpoints preserved: [Yes/No]
- ✅ Migration is additive: [Yes/No]
- ✅ Existing tests still pass: [Will verify]

### Risk Assessment:
- Low Risk: Only additive changes
- Medium Risk: Modifying existing methods (with backward compat)
- High Risk: Breaking changes (should not occur)

---

## 6. Bug Analysis
[For FIX mode only]

### Symptoms:
[Detailed bug description from user]

### Layer Detection:
**Affected Layer:** [Domain/Service/API/Repository]

**Indicators:**
- [List symptoms that point to this layer]
- [Error messages that confirm layer]

**Root Cause:**
[Detailed explanation of what's wrong]

### Fix Strategy:
1. Write failing test that reproduces bug
2. [Specific fix to apply]
3. Verify all tests pass (including regression)

---

## 7. Dependency Validation

### Expected Dependencies:
- Domain → NOTHING ✓
- Contracts → NOTHING ✓
- Application → Domain + Contracts ✓
- Infrastructure → Domain ✓
- API → Application + Contracts ✓

### Validation Method:
Will verify using namespace analysis after implementation.

---

## 8. Success Criteria

### Must Achieve:
- [ ] All unit tests pass (100%)
- [ ] All E2E tests pass (100%)
- [ ] Build succeeds with zero errors
- [ ] Migration created and applied
- [ ] Dependencies correct
- [ ] DDD patterns followed
- [ ] TDD discipline maintained
- [ ] No unused files remain
- [ ] Backward compatibility preserved (ENHANCE mode)
- [ ] Bug reproduced and fixed (FIX mode)

### Completion Definition:
This plan is complete when ALL success criteria are met and verified.

---

## 9. Timeline & Phases

### Phase 5: Implementation
- Domain layer (TDD)
- Contracts layer
- Infrastructure layer
- Application layer
- API layer
- Service registration
- Migration

### Phase 6: Validation & Cleanup
- Test verification
- Build verification
- Dependency check
- Unused file detection
- Cleanup execution

**Total Checkpoints:** 6 interactive approval points

---

## 10. Notes & Context

[Any additional notes, context, or decisions made during planning]

---

**Plan Status:** PENDING APPROVAL

This plan will be updated to "IN PROGRESS" once user approves, and "COMPLETED" when all success criteria are met.
```

**Save this plan file BEFORE asking user for approval to proceed.**

User can reference this plan later at: `ProjectArchitecture/Plans/[filename].md`

---

### For CREATE Mode - Files to Create:

**Domain Layer:**
- `HammondPoleInc.Domain/Entities/[Context]/[Entity].cs` - Aggregate root entity
- `HammondPoleInc.Domain/Interfaces/Repositories/I[Entity]Repository.cs` - Repository interface
- `HammondPoleInc.Domain/ValueObjects/[ValueObject].cs` - Any value objects (if specified)
- `HammondPoleInc.Domain/Events/[Context]/[Entity]CreatedEvent.cs` - Domain events
- `HammondPoleInc.Domain/Events/[Context]/[Entity]UpdatedEvent.cs`

**Infrastructure Layer:**
- `HammondPoleInc.Infrastructure/Repositories/[Entity]Repository.cs` - Repository implementation
- `HammondPoleInc.Infrastructure/Data/Configurations/[Entity]Configuration.cs` - EF Core configuration
- Migration file (to be generated)

**Application Layer:**
- `HammondPoleInc.Application/Interfaces/I[Entity]Service.cs` - Service interface
- `HammondPoleInc.Application/Services/[Entity]Service.cs` - Service implementation

**Contracts Layer:**
- `HammondPoleInc.Contracts/DTOs/[Context]/[Entity]Dto.cs` - Response DTO
- `HammondPoleInc.Contracts/DTOs/[Context]/[Entity]SummaryDto.cs` - Summary DTO
- `HammondPoleInc.Contracts/DTOs/[Context]/Create[Entity]Request.cs` - Create request
- `HammondPoleInc.Contracts/DTOs/[Context]/Update[Entity]Request.cs` - Update request
- `HammondPoleInc.Contracts/DTOs/[Context]/[Entity]PaginationQuery.cs` - Pagination query (if list operation)

**API Layer:**
- `HammondPoleInc.API/Endpoints/[Context]/[Entity]Endpoints.cs` - Minimal API endpoints

**Service Registration:**
- Update `HammondPoleInc.ServiceDefaults/Extensions.cs` or appropriate DI registration file

**Test Layer:**
- `HammondPoleInc.Tests.Unit/Domain/Entities/[Context]/[Entity]Tests.cs` - Domain entity unit tests
- `HammondPoleInc.Frontend/tests/e2e/[entity]-api.spec.ts` - Playwright E2E API tests

**After creating the plan, save it to:**
```
ProjectArchitecture/Plans/CREATE-[Entity]-[YYYYMMDD-HHMMSS].md
```

**Then show CREATE plan to user and ask:** "Does this implementation plan look correct? I've saved it to `ProjectArchitecture/Plans/CREATE-[Entity]-[Timestamp].md` for future reference. Should I proceed?"

---

### For ENHANCE Mode - Files to Modify/Create:

**Files to Modify (Existing):**
- `HammondPoleInc.Domain/Entities/[Context]/[Entity].cs` - Add new properties, methods, relationships
- `HammondPoleInc.Domain/Interfaces/Repositories/I[Entity]Repository.cs` - Add new query methods (if needed)
- `HammondPoleInc.Infrastructure/Repositories/[Entity]Repository.cs` - Implement new query methods
- `HammondPoleInc.Infrastructure/Data/Configurations/[Entity]Configuration.cs` - Configure new properties/relationships
- `HammondPoleInc.Application/Interfaces/I[Entity]Service.cs` - Add new service methods
- `HammondPoleInc.Application/Services/[Entity]Service.cs` - Implement new operations
- `HammondPoleInc.API/Endpoints/[Context]/[Entity]Endpoints.cs` - Add new endpoints (if requested)

**Files to Create (New):**
- `HammondPoleInc.Domain/Events/[Context]/[Entity][NewOperation]Event.cs` - New domain events
- `HammondPoleInc.Contracts/DTOs/[Context]/[NewOperation][Entity]Request.cs` - New request DTOs (if new endpoints)
- Migration file (if database changes)

**Tests to Modify/Create:**
- `HammondPoleInc.Tests.Unit/Domain/Entities/[Context]/[Entity]Tests.cs` - Add tests for new functionality
- `HammondPoleInc.Frontend/tests/e2e/[entity]-api.spec.ts` - Add E2E tests for new endpoints

**Backward Compatibility Checks:**
- ✅ New properties are nullable or have defaults (non-breaking)
- ✅ Existing endpoints unchanged (non-breaking)
- ✅ New endpoints are additions only (non-breaking)
- ✅ Existing tests still pass after changes

**After creating the plan, save it to:**
```
ProjectArchitecture/Plans/ENHANCE-[Entity]-[YYYYMMDD-HHMMSS].md
```

**Then show ENHANCE plan to user and ask:** "Does this enhancement plan look correct? I've saved it to `ProjectArchitecture/Plans/ENHANCE-[Entity]-[Timestamp].md` for future reference. Should I proceed?"

---

### For FIX Mode - Bug Analysis & Fix Plan:

**Step 1: Automatic Layer Detection**

Based on bug description, error messages, and symptoms, analyze which layer has the issue:

**Indicators by Layer:**

*Domain Layer Issues:*
- Business rule validation failing
- Domain exception being thrown
- Invalid state allowed
- Example: "User can set self as parent" → Domain validation missing

*Service Layer Issues:*
- Missing validation before saving
- Incorrect orchestration logic
- Wrong exception type being thrown
- Example: "Duplicate email returns 500" → Service not checking existence

*API Layer Issues:*
- Wrong HTTP status code
- Incorrect response format
- Missing authorization check
- Example: "Returns 500 instead of 400" → API error handling issue

*Repository Layer Issues:*
- Query returning wrong data
- Include/navigation issues
- Database connection errors
- Example: "Children not loaded" → Missing Include()

**Step 2: Show Analysis to User**

```
Bug Analysis:
- Affected Layer: [Domain/Service/API/Repository]
- Root Cause: [Explanation]
- Symptoms Match: [List matching indicators]
```

**Step 3: Fix Plan**

**Files to Modify:**
- [Specific file identified] - [What needs to change]

**Tests to Create/Modify:**
- Write failing test that reproduces the bug (RED)
- Fix the bug (GREEN)
- Refactor if needed (REFACTOR)

**Regression Prevention:**
- ✅ Existing tests still pass
- ✅ New test prevents bug from recurring

**After creating the analysis and fix plan, save it to:**
```
ProjectArchitecture/Plans/FIX-[Entity]-[BugSummary]-[YYYYMMDD-HHMMSS].md
```

**Then show FIX plan to user and ask:** "Does this bug analysis and fix plan look correct? I've saved it to `ProjectArchitecture/Plans/FIX-[Entity]-[BugSummary]-[Timestamp].md` for future reference. Should I proceed?"

---

## PHASE 4: TEST-DRIVEN DEVELOPMENT (TDD)

**CRITICAL: Follow TDD Red-Green-Refactor Cycle**

This skill follows **Test-Driven Development**. For each component:
1. **🔴 RED**: Write the test first (it will fail)
2. **🟢 GREEN**: Write minimal code to make the test pass
3. **🔵 REFACTOR**: Improve the code while keeping tests green

### TDD Benefits:
✅ Tests define the contract before implementation
✅ Ensures 100% test coverage
✅ Catches design issues early
✅ Provides living documentation
✅ Prevents regression

---

## PHASE 5: IMPLEMENTATION

**CRITICAL: Read the DDD Pattern Guide first:**
```
Read file: ProjectArchitecture\DDD-Pattern-Guide.md
```

Use this guide as the source of truth for all patterns, naming conventions, and best practices.

### Implementation Order (MUST follow TDD - Test First!):

---

#### Step 1: 🔴 RED - Write Domain Entity Tests First

**File:** `HammondPoleInc.Tests.Unit/Domain/Entities/[Context]/[Entity]Tests.cs`

Create comprehensive unit tests for the domain entity BEFORE implementing it:

**1.1 Test Structure:**
```csharp
using HammondPoleInc.Domain.Entities.[Context];
using HammondPoleInc.Domain.Exceptions;
using Xunit;

namespace HammondPoleInc.Tests.Unit.Domain.Entities.[Context];

public class [Entity]Tests
{
    // Factory Method Tests
    [Fact]
    public void Create_WithValidData_ShouldCreateEntity() { }

    [Fact]
    public void Create_WithInvalidData_ShouldThrowDomainException() { }

    // Business Rule Tests
    [Fact]
    public void IsValid_WhenAllRulesPass_ShouldReturnTrue() { }

    [Fact]
    public void IsValid_WhenRuleFails_ShouldReturnFalse() { }

    // Domain Operation Tests
    [Fact]
    public void CustomOperation_WithValidInput_ShouldSucceed() { }

    [Fact]
    public void CustomOperation_WithInvalidInput_ShouldThrowException() { }

    // Domain Event Tests
    [Fact]
    public void Create_ShouldRaise_EntityCreatedEvent() { }
}
```

**1.2 Write Tests for:**
- ✅ Factory methods (Create, CreateFrom*)
- ✅ Each business rule specified by user
- ✅ Each custom operation specified by user
- ✅ Required property validation
- ✅ Domain events are raised
- ✅ Invalid state throws DomainException

**1.3 Run Tests:**
```bash
dotnet test HammondPoleInc.Tests.Unit --filter "[Entity]Tests"
```
**Expected Result:** All tests FAIL (Red) because entity doesn't exist yet.

---

#### Step 2: 🟢 GREEN - Implement Domain Layer to Pass Tests

**2.1 Create Value Objects** (if any):
- Immutable classes with validation
- Private constructor + static Create method
- Override Equals for value comparison
- Reference: DDD-Pattern-Guide.md "Value Objects" section
- **Run related tests** - should turn green

**2.2 Create Domain Events**:
- Record types inheriting from DomainEvent
- Include all relevant data for the event
- Naming: `[Entity][PastTense]Event` (e.g., ProjectCreatedEvent)
- Reference: DDD-Pattern-Guide.md "Domain Events" section

**2.3 Create Entity (Aggregate Root)**:
- Implement ONLY what's needed to pass the tests
- Inherit from IdentityUser<Guid> if user-related, otherwise just properties + IAggregateRoot marker
- Include all properties from user input
- Add navigation properties for relationships
- Implement business rule methods (IsValid*, CanDelete, etc.)
- Add factory methods (Create, CreateFrom*)
- Add business operation methods (Assign*, Update*, etc.)
- Raise domain events in factory and operation methods
- Add domain event collection (private List<DomainEvent> _domainEvents)
- Reference: DDD-Pattern-Guide.md "Entities" section

**2.4 Run Tests Again:**
```bash
dotnet test HammondPoleInc.Tests.Unit --filter "[Entity]Tests"
```
**Expected Result:** All tests PASS (Green)

**2.5 Create Repository Interface**:
- Interface name: `I[Entity]Repository`
- Core CRUD methods: GetByIdAsync, GetAllAsync, AddAsync, UpdateAsync, DeleteAsync, SaveChangesAsync
- Domain-specific queries based on user requirements
- Existence checks: ExistsAsync methods
- Reference: DDD-Pattern-Guide.md "Repositories" section

---

#### Step 3: 🔵 REFACTOR - Improve Domain Code

**3.1 Review entity code for:**
- ✅ Code duplication
- ✅ Clear method names
- ✅ Proper encapsulation
- ✅ Single Responsibility Principle

**3.2 Refactor while keeping tests green**
- Run tests after each refactoring
- Tests should still pass

---

#### Step 4: Contracts Layer (No Dependencies)

**4.1 Create DTOs**:
- `[Entity]Dto` - Full response DTO with all properties
- `[Entity]SummaryDto` - Lightweight DTO for lists
- Include navigation property DTOs (e.g., DepartmentSummaryDto)
- Reference: DDD-Pattern-Guide.md "Contracts Layer" section

**4.2 Create Request DTOs**:
- `Create[Entity]Request` - Properties for creation
- `Update[Entity]Request` - Properties for updates (nullable for partial updates)
- Validation attributes if needed

**4.3 Create Query DTOs** (if pagination needed):
- `[Entity]PaginationQuery` - Page, PageSize, Search, Filters, OrderBy, OrderDesc

---

#### Step 5: Infrastructure Layer (Depends on Domain)

**5.1 Create EF Core Configuration**:
- File: `[Entity]Configuration.cs`
- Implement IEntityTypeConfiguration<[Entity]>
- Configure table name, primary key, properties, indexes, relationships
- Add check constraints for business rules
- Reference existing configurations (e.g., ApplicationUserConfiguration.cs)

**5.2 Create Repository Implementation**:
- Implement I[Entity]Repository interface
- Use ApplicationDbContext
- Implement all interface methods
- Use Include() for navigation properties
- Use AsNoTracking() for read-only queries
- Reference: Existing repositories in Infrastructure/Repositories/

**5.3 Update DbContext**:
- Add DbSet<[Entity]> property to ApplicationDbContext
- Apply configuration in OnModelCreating

---

#### Step 6: Application Layer (Depends on Domain + Contracts)

**6.1 Create Service Interface**:
- Interface name: `I[Entity]Service`
- Methods return DTOs (from Contracts layer)
- Methods accept Request DTOs (from Contracts layer)
- Include all CRUD operations user requested
- Include custom operations user specified

**6.2 Create Service Implementation**:
- Inject I[Entity]Repository and any related repositories
- Inject ILogger<[Entity]Service>
- Implement all interface methods
- Pattern for each method:
  1. Get entity from repository (if updating/deleting)
  2. Validate business rules (check duplicates, validate related entities)
  3. Use entity factory methods or domain methods
  4. Call domain validation methods (IsValid*)
  5. Save via repository
  6. Transform to DTO and return
- Private helper method: `TransformToDto(Entity entity)` to map entity → DTO
- Reference: UserService.cs as example

---

#### Step 7: API Layer (Depends on Application + Contracts)

**7.1 Create Endpoints**:
- Implement IEndpointDefinition
- DefineEndpoints method with MapGroup
- Create endpoint methods (private static async Task<IResult>)
- Inject services via [FromServices]
- Return SuccessResponse<T> or ErrorResponse
- Add authorization if user requested (.RequireAuthorization() or .RequireAuthorization("PERMISSION_NAME"))
- Reference: UserEndpoints.cs as example

---

#### Step 8: Service Registration

**8.1 Register Services**:
- Add to appropriate service collection extension
- `services.AddScoped<I[Entity]Repository, [Entity]Repository>();`
- `services.AddScoped<I[Entity]Service, [Entity]Service>();`

---

#### Step 9: Database Migration

**9.1 Create Migration**:
```bash
dotnet ef migrations add Add[Entity]Aggregate --project HammondPoleInc.API
```

**9.2 Review migration file** to ensure it matches expected schema

**9.3 Apply Migration**:
```bash
dotnet ef database update --project HammondPoleInc.API
```

---

#### Step 10: 🔴 RED - Write Playwright E2E Tests for API Endpoints

**File:** `HammondPoleInc.Frontend/tests/e2e/[entity]-api.spec.ts`

Write E2E tests for API endpoints BEFORE manually testing:

**10.1 Test Structure:**
```typescript
import { test, expect } from '@playwright/test';

const API_BASE_URL = 'http://localhost:7100';
const ENTITY_ENDPOINT = '/api/[entities]';

test.describe('[Entity] API Endpoints', () => {
  let authToken: string;
  let createdEntityId: string;

  // Setup: Get auth token
  test.beforeAll(async ({ request }) => {
    // Get auth token or use test user token
    const response = await request.post(`${API_BASE_URL}/api/auth/token`, {
      data: { /* test credentials */ }
    });
    const data = await response.json();
    authToken = data.token;
  });

  // Test: Create Entity
  test('POST /api/[entities] - should create entity', async ({ request }) => {
    const response = await request.post(`${API_BASE_URL}${ENTITY_ENDPOINT}`, {
      headers: { 'Authorization': `Bearer ${authToken}` },
      data: { /* create request */ }
    });

    expect(response.status()).toBe(200);
    const data = await response.json();
    expect(data.success).toBe(true);
    createdEntityId = data.data.id;
  });

  // Test: Get Entity by ID
  test('GET /api/[entities]/{id} - should get entity by id', async ({ request }) => {
    const response = await request.get(
      `${API_BASE_URL}${ENTITY_ENDPOINT}/${createdEntityId}`,
      { headers: { 'Authorization': `Bearer ${authToken}` } }
    );

    expect(response.status()).toBe(200);
    const data = await response.json();
    expect(data.data.id).toBe(createdEntityId);
  });

  // Test: Update Entity
  test('PUT /api/[entities]/{id} - should update entity', async ({ request }) => {
    const response = await request.put(
      `${API_BASE_URL}${ENTITY_ENDPOINT}/${createdEntityId}`,
      {
        headers: { 'Authorization': `Bearer ${authToken}` },
        data: { /* update request */ }
      }
    );

    expect(response.status()).toBe(200);
    const data = await response.json();
    expect(data.success).toBe(true);
  });

  // Test: List Entities
  test('GET /api/[entities] - should list entities', async ({ request }) => {
    const response = await request.get(`${API_BASE_URL}${ENTITY_ENDPOINT}`, {
      headers: { 'Authorization': `Bearer ${authToken}` }
    });

    expect(response.status()).toBe(200);
    const data = await response.json();
    expect(Array.isArray(data.data)).toBe(true);
  });

  // Test: Delete Entity
  test('DELETE /api/[entities]/{id} - should delete entity', async ({ request }) => {
    const response = await request.delete(
      `${API_BASE_URL}${ENTITY_ENDPOINT}/${createdEntityId}`,
      { headers: { 'Authorization': `Bearer ${authToken}` } }
    );

    expect(response.status()).toBe(200);
  });

  // Test: Business Rule Validation
  test('POST /api/[entities] - should reject invalid data', async ({ request }) => {
    const response = await request.post(`${API_BASE_URL}${ENTITY_ENDPOINT}`, {
      headers: { 'Authorization': `Bearer ${authToken}` },
      data: { /* invalid data */ }
    });

    expect(response.status()).toBe(400); // or appropriate error status
  });
});
```

**10.2 Write Tests for:**
- ✅ Each CRUD endpoint
- ✅ Each custom operation endpoint
- ✅ Business rule validation (should reject invalid data)
- ✅ Authorization (should return 401 without token)
- ✅ Pagination (if applicable)

**10.3 Run E2E Tests:**
```bash
npm run test:e2e -- [entity]-api.spec.ts
```
**Expected Result:** Tests FAIL (Red) because API isn't running or has issues.

---

#### Step 11: 🟢 GREEN - Fix Issues to Pass E2E Tests

**11.1 Start the API:**
```bash
dotnet run --project HammondPoleInc.API
```

**11.2 Run E2E Tests Again:**
```bash
npm run test:e2e -- [entity]-api.spec.ts
```

**11.3 Fix any failures:**
- API endpoint not found → Check endpoint registration
- Validation errors → Fix request DTOs
- Authorization errors → Check permission configuration
- Business rule errors → Fix domain logic

**11.4 Repeat until all tests PASS (Green)**

---

#### Step 12: 🔵 REFACTOR - Improve API Code

**12.1 Review endpoint code for:**
- ✅ Consistent error handling
- ✅ Clear response messages
- ✅ Proper status codes
- ✅ Validation feedback

**12.2 Refactor while keeping E2E tests green**
- Run tests after each refactoring

---

### ENHANCE MODE Implementation (Steps 1-12 Modified for Enhancements)

**Implementation follows same TDD cycle but modifies existing files:**

#### Step 1: 🔴 RED - Write Tests for New Functionality

**Update existing test file:** `HammondPoleInc.Tests.Unit/Domain/Entities/[Context]/[Entity]Tests.cs`

Add new test methods for:
- New properties validation
- New business rules
- New domain operations
- Domain events for new operations

Run tests - new tests should FAIL (no implementation yet).

#### Step 2-3: 🟢 GREEN + 🔵 REFACTOR - Update Domain Entity

**Modify:** `HammondPoleInc.Domain/Entities/[Context]/[Entity].cs`

- Add new properties (ensure nullable or with defaults for backward compatibility)
- Add new business rule methods
- Add new operation methods
- Raise domain events for new operations
- Update existing methods if needed (carefully - backward compatibility!)

Run tests - all tests should PASS (including existing ones!).

#### Step 4: Update Contracts Layer

**Create new DTOs if needed:**
- New request DTOs for new operations
- Update existing DTOs to include new properties (nullable!)

#### Step 5: Update Infrastructure Layer

**Modify:** `[Entity]Configuration.cs`
- Configure new properties
- Configure new relationships
- Add indexes if needed

**Modify:** `I[Entity]Repository.cs` and `[Entity]Repository.cs`
- Add new query methods if needed

**Update DbContext** if new relationships added.

#### Step 6: Update Application Layer

**Modify:** `I[Entity]Service.cs`
- Add new method signatures

**Modify:** `[Entity]Service.cs`
- Implement new methods
- Use new domain operations
- Transform to DTOs

#### Step 7: Update API Layer (if new endpoints)

**Modify:** `[Entity]Endpoints.cs`
- Add new endpoint methods
- Map new routes

#### Step 8: Service Registration (if needed)
- Usually no changes needed

#### Step 9: Create Migration

```bash
dotnet ef migrations add Add[NewFeature]To[Entity] --project HammondPoleInc.API
dotnet ef database update --project HammondPoleInc.API
```

#### Step 10-12: 🔴🟢🔵 TDD for New E2E Tests

**Update:** `[entity]-api.spec.ts`
- Add tests for new endpoints (if any)
- Run and ensure all tests pass (new and existing!)

---

### FIX MODE Implementation (Bug-Specific Steps)

**Implementation targets the specific layer with the bug:**

#### Step 1: 🔴 RED - Write Failing Test That Reproduces Bug

Based on layer detection from PHASE 3:

**If Domain Layer Bug:**
- Add test to `[Entity]Tests.cs` that reproduces the bug
- Test should FAIL (demonstrates the bug)

**If Service Layer Bug:**
- Add integration test or modify existing test
- Test should FAIL (demonstrates the bug)

**If API Layer Bug:**
- Add E2E test to `[entity]-api.spec.ts` that reproduces the bug
- Test should FAIL (demonstrates the bug)

**If Repository Layer Bug:**
- Add repository test that demonstrates wrong query behavior
- Test should FAIL

Run the new test - confirm it FAILS.

#### Step 2: 🟢 GREEN - Fix the Bug

**Modify the identified file(s):**

*Domain Entity Fix:*
- Add missing validation
- Fix business rule logic
- Add missing domain event

*Service Fix:*
- Add missing validation check
- Fix orchestration logic
- Handle exceptions correctly

*API Fix:*
- Fix status code
- Fix response format
- Add missing authorization

*Repository Fix:*
- Add missing Include()
- Fix query logic
- Fix filtering

Run the failing test - should now PASS.

#### Step 3: Regression Testing

Run ALL existing tests to ensure no regression:
```bash
dotnet test HammondPoleInc.Tests.Unit
npm run test:e2e
```

All tests should PASS.

#### Step 4: 🔵 REFACTOR - Improve Code (Optional)

If bug was caused by poor code structure:
- Refactor while keeping tests green
- Improve clarity
- Add documentation

#### Step 5: Migration (if database fix)

Only if fix requires schema change:
```bash
dotnet ef migrations add Fix[BugDescription] --project HammondPoleInc.API
dotnet ef database update --project HammondPoleInc.API
```

---

## PHASE 6: VALIDATION & CLEANUP (All Modes)

### File Tracking:
Maintain a list of all files created during implementation.

### Validation Steps:

1. **Test Verification** (CRITICAL - TDD):
   ```bash
   # Run domain entity tests
   dotnet test HammondPoleInc.Tests.Unit --filter "[Entity]Tests"

   # Run E2E API tests
   npm run test:e2e -- [entity]-api.spec.ts
   ```
   - ✅ All domain entity tests PASS (Green)
   - ✅ All E2E API tests PASS (Green)
   - If any tests fail, fix immediately before proceeding

2. **Build Verification**:
   ```bash
   dotnet build
   ```
   - If build fails, fix errors immediately
   - Common issues: Missing usings, namespace mismatches, circular dependencies

3. **Dependency Check**:
   - ✅ Domain has NO dependencies on other layers
   - ✅ Contracts has NO dependencies on other layers
   - ✅ Application depends ONLY on Domain + Contracts
   - ✅ Infrastructure depends ONLY on Domain
   - ✅ API depends ONLY on Application + Contracts

4. **Pattern Compliance**:
   - ✅ Entity has factory methods (Create, CreateFrom*)
   - ✅ Entity has business rule methods (IsValid*, Can*)
   - ✅ Entity raises domain events
   - ✅ Repository interface in Domain, implementation in Infrastructure
   - ✅ Service methods return DTOs, not entities
   - ✅ API endpoints use SuccessResponse<T> wrapper
   - ✅ All tests written BEFORE implementation (TDD)

5. **Unused File Detection**:
   - Check if any created files are not referenced anywhere
   - Common unused files:
     - Value objects that were created but not used in entity
     - DTOs that duplicate existing ones
     - Service methods that weren't wired to endpoints
     - Test files that don't run or are empty

6. **Cleanup**:
   - Delete any unused files identified
   - Remove from project file if needed
   - Provide summary: "Cleaned up X unused files: [list]"

7. **Update Plan Status**:
   - Open the saved plan file: `ProjectArchitecture/Plans/[Mode]-[Entity]-[Timestamp].md`
   - Update status from "PENDING APPROVAL" → "COMPLETED"
   - Add completion timestamp
   - Add final results summary (files created, tests passed, etc.)
   - Save updated plan

8. **Create Finalized Backend Report for Frontend**:
   - Create concise frontend-focused report
   - Save to: `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`
   - Include: API endpoints, DTOs, request/response formats, auth requirements, business rules
   - This report is used by frontend developers and the react-feature-builder skill

### Final Report (Mode-Specific):

---

#### CREATE Mode Report:

**📋 Implementation Plan Saved:**
- Plan document: `ProjectArchitecture/Plans/CREATE-[Entity]-[Timestamp].md`
- Reference this plan for future modifications or documentation

**📁 Files Created:**
- Domain Layer: X files
- Infrastructure Layer: X files
- Application Layer: X files
- Contracts Layer: X files
- API Layer: X files
- Tests: X files (unit + E2E)

**🧹 Files Cleaned Up:**
- X unused files removed: [list]

**🗄️ Database:**
- ✅ Migration created and applied

**🔨 Build Status:**
- ✅ `dotnet build` passed

**✅ Test Results:**
- ✅ Domain Entity Tests: X/X passed
- ✅ E2E API Tests: X/X passed
- ✅ Test Coverage: XX%

**📊 TDD Summary:**
- 🔴 RED: Tests written first
- 🟢 GREEN: Implementation passed all tests
- 🔵 REFACTOR: Code improved while maintaining green tests

**📄 Frontend Integration Report:**
- Backend API report saved: `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`
- This report contains all API endpoints, DTOs, and integration details for frontend developers

**🚀 Next Steps:**
- Feature ready: `[Entity]` aggregate created
- API endpoints: `http://localhost:7100/api/[entities]`
- All tests passing
- Implementation plan saved for reference
- **Frontend team: Read the backend report for integration details**
- Ready for frontend integration (use `react-feature-builder` skill)

---

#### ENHANCE Mode Report:

**📋 Implementation Plan Saved:**
- Plan document: `ProjectArchitecture/Plans/ENHANCE-[Entity]-[Timestamp].md`
- Reference this plan to see what was added and backward compatibility decisions

**📝 Files Modified:**
- Domain Layer: X files updated
- Infrastructure Layer: X files updated
- Application Layer: X files updated
- Contracts Layer: X files updated
- API Layer: X files updated (if new endpoints)

**📁 Files Created:**
- New DTOs: X files
- New domain events: X files
- New tests: X files

**🧹 Files Cleaned Up:**
- X unused files removed: [list]

**🗄️ Database:**
- ✅ Migration created and applied (if schema changes)

**🔨 Build Status:**
- ✅ `dotnet build` passed

**✅ Test Results:**
- ✅ Existing Tests Still Pass: X/X ✓ (regression check)
- ✅ New Tests Pass: X/X ✓
- ✅ Total Test Coverage: XX%

**🔄 Backward Compatibility:**
- ✅ Existing endpoints unchanged
- ✅ New properties are nullable/have defaults
- ✅ No breaking changes introduced

**📊 TDD Summary:**
- 🔴 RED: New tests written first (failed)
- 🟢 GREEN: New functionality implemented (tests pass)
- 🔵 REFACTOR: Code improved while keeping all tests green

**📄 Frontend Integration Report:**
- Backend API report updated: `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`
- Report now includes new endpoints and updated DTOs for enhanced functionality

**🚀 Next Steps:**
- Enhancement complete: New features added to `[Entity]`
- New endpoints (if any): [list]
- All existing functionality preserved
- **Frontend team: Review updated backend report for new API features**
- Ready for frontend integration of new features

---

#### FIX Mode Report:

**📋 Bug Analysis & Fix Plan Saved:**
- Plan document: `ProjectArchitecture/Plans/FIX-[Entity]-[BugSummary]-[Timestamp].md`
- Reference this plan to understand root cause analysis and fix approach

**🐛 Bug Fixed:**
- **Feature:** [Entity]
- **Layer:** [Domain/Service/API/Repository]
- **Root Cause:** [Explanation]
- **Fix Applied:** [What was changed]

**📝 Files Modified:**
- [List of files changed]

**✅ Test Results:**
- ✅ Bug Reproduction Test: FAILS before fix → PASSES after fix ✓
- ✅ Regression Tests: All existing tests still pass X/X ✓
- ✅ Additional Tests Added: X tests

**🗄️ Database:**
- Migration created and applied (only if schema fix needed)

**🔨 Build Status:**
- ✅ `dotnet build` passed

**📊 TDD Summary:**
- 🔴 RED: Wrote failing test that reproduced bug
- 🟢 GREEN: Fixed bug, test now passes
- 🔵 REFACTOR: Improved code to prevent similar bugs

**🛡️ Prevention:**
- ✅ Test added prevents bug from recurring
- ✅ Documentation updated (if needed)
- ✅ Similar code patterns reviewed

**📄 Frontend Integration Report:**
- Backend API report reviewed: `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`
- Report updated if API contract changed (rare for bug fixes)
- Note added if frontend needs to be aware of behavior change

**🚀 Next Steps:**
- Bug fixed and verified
- Regression prevented
- **Frontend team: Review if API behavior changed**
- Ready for deployment

---

## IMPORTANT REMINDERS

### TDD Non-Negotiables:
1. **🔴 ALWAYS write tests FIRST** - Never write implementation before tests
2. **🟢 ALWAYS run tests after implementation** - Ensure they pass
3. **🔵 ALWAYS run tests after refactoring** - Ensure they stay green
4. **❌ NEVER skip the RED phase** - Tests must fail before you implement

### DDD Non-Negotiables:
1. **ALWAYS read DDD-Pattern-Guide.md before implementing**
2. **ALWAYS ask ALL questions in Phase 1 before coding**
3. **ALWAYS follow the implementation order exactly**
4. **ALWAYS validate dependencies point inward**
5. **ALWAYS clean up unused files**
6. **NEVER expose domain entities to API layer**
7. **NEVER put business logic in Application or Infrastructure**
8. **NEVER create repositories for non-aggregate-root entities**

### Quality Gates:
- ✅ All unit tests pass
- ✅ All E2E tests pass
- ✅ Build succeeds
- ✅ Dependencies are correct
- ✅ DDD patterns followed
- ✅ No unused files remain
- ✅ Backend report created for frontend team

---

## PHASE 7: FINALIZED BACKEND REPORT (Frontend Integration)

**CRITICAL: Create a frontend-focused API report after all validation passes.**

### Report File:

**Location:** `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`

**File Name Pattern:**
- CREATE mode: `[Entity]-Backend-API.md` (e.g., `Project-Backend-API.md`)
- ENHANCE mode: Update existing `[Entity]-Backend-API.md` (append new features)
- FIX mode: Update existing `[Entity]-Backend-API.md` only if API contract changed

### Report Structure:

```markdown
# [Entity] Backend API Reference

**Last Updated:** [Timestamp]
**Status:** Production Ready
**Base URL:** `http://localhost:7100` (development) | `https://api.hammondpole.com` (production)

---

## Quick Summary

**Feature:** [Brief description of what this feature does]
**Aggregate:** [Entity name]
**Endpoints Available:** [Number] endpoints
**Authentication Required:** Yes/No

---

## API Endpoints

### Endpoint Overview

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/[entities]` | Create new [entity] | Yes |
| GET | `/api/[entities]/{id}` | Get [entity] by ID | Yes |
| GET | `/api/[entities]` | List all [entities] | Yes |
| PUT | `/api/[entities]/{id}` | Update [entity] | Yes |
| DELETE | `/api/[entities]/{id}` | Delete [entity] | Yes |

### Detailed Endpoint Documentation

#### 1. Create [Entity]

**Endpoint:** `POST /api/[entities]`

**Authentication:** Required (Permission: [PERMISSION_NAME])

**Request Body:**
\`\`\`typescript
interface Create[Entity]Request {
  propertyName1: string;           // Description, validation rules
  propertyName2: number;           // Description, validation rules
  propertyName3?: string | null;   // Optional property
  departmentId?: number;           // Foreign key to Department
}
\`\`\`

**Response (Success - 200):**
\`\`\`typescript
interface SuccessResponse<[Entity]Dto> {
  success: true;
  data: {
    id: string;                    // GUID
    propertyName1: string;
    propertyName2: number;
    propertyName3: string | null;
    createdDate: string;           // ISO 8601 DateTime
    // ... all entity properties
  };
  message: string;                 // e.g., "[Entity] created successfully"
}
\`\`\`

**Response (Error - 400):**
\`\`\`typescript
interface ErrorResponse {
  success: false;
  message: string;                 // e.g., "Validation failed: PropertyName1 is required"
  errorCode: string;               // e.g., "VALIDATION_ERROR"
}
\`\`\`

**Business Rules Enforced:**
- Rule 1: [Description - e.g., "Budget cannot be negative"]
- Rule 2: [Description - e.g., "Email must be unique"]

**Example Request:**
\`\`\`json
{
  "name": "New Project",
  "description": "Construction project for building renovation",
  "budget": 50000.00,
  "departmentId": 1
}
\`\`\`

---

#### 2. Get [Entity] by ID

**Endpoint:** `GET /api/[entities]/{id}`

**Parameters:**
- `id` (path) - GUID of the [entity]

**Response (Success - 200):**
\`\`\`typescript
interface SuccessResponse<[Entity]Dto> {
  success: true;
  data: [Entity]Dto;  // Full entity details with navigation properties
  message: string;
}
\`\`\`

**Response (Not Found - 404):**
\`\`\`typescript
interface ErrorResponse {
  success: false;
  message: string;                 // e.g., "[Entity] not found"
  errorCode: string;               // "NOT_FOUND"
}
\`\`\`

---

#### 3. List [Entities]

**Endpoint:** `GET /api/[entities]`

**Query Parameters:**
- `page` (optional, default: 1) - Page number
- `pageSize` (optional, default: 20) - Items per page (10, 20, 50)
- `search` (optional) - Text search across all fields
- `orderBy` (optional, default: "name") - Sort field
- `orderDesc` (optional, default: false) - Sort descending

**Response (Success - 200):**
\`\`\`typescript
interface PaginatedResponse<[Entity]Dto> {
  success: true;
  data: {
    items: [Entity]Dto[];
    totalCount: number;
    page: number;
    pageSize: number;
    totalPages: number;
    hasPrevious: boolean;
    hasNext: boolean;
  };
  message: string;
}
\`\`\`

---

## Data Structures (DTOs)

### [Entity]Dto (Full Details)

\`\`\`typescript
interface [Entity]Dto {
  id: string;                      // GUID
  propertyName1: string;
  propertyName2: number;
  propertyName3: string | null;    // Nullable property
  createdDate: string;             // ISO 8601 DateTime
  lastModifiedDate: string | null; // ISO 8601 DateTime

  // Navigation properties (populated when included)
  department: DepartmentSummaryDto | null;
  assignedUser: UserSummaryDto | null;
}
\`\`\`

### [Entity]SummaryDto (Lightweight)

Used in lists and dropdown selections:

\`\`\`typescript
interface [Entity]SummaryDto {
  id: string;                      // GUID
  name: string;                    // Display name
  code: string;                    // Short identifier
  isActive: boolean;
}
\`\`\`

### Related DTOs

**DepartmentSummaryDto:**
\`\`\`typescript
interface DepartmentSummaryDto {
  id: number;
  name: string;
  code: string;
  isActive: boolean;
}
\`\`\`

**UserSummaryDto:**
\`\`\`typescript
interface UserSummaryDto {
  id: string;                      // GUID
  displayName: string;
  email: string;
}
\`\`\`

---

## Authentication & Authorization

### Authentication Method:

**Type:** Bearer Token (JWT from Microsoft Entra ID / Azure AD)

**Header Required:**
\`\`\`http
Authorization: Bearer <MSAL_ACCESS_TOKEN>
\`\`\`

### Permissions:

| Permission | Endpoints | Description |
|------------|-----------|-------------|
| [PERMISSION_NAME] | All [entity] endpoints | Full CRUD access to [entities] |

### Getting a Token (MSAL):

\`\`\`typescript
import { useMsal } from '@azure/msal-react';

const { instance, accounts } = useMsal();

const token = await instance.acquireTokenSilent({
  scopes: ['api://your-api-scope'],
  account: accounts[0]
});

// Use token.accessToken in API calls
\`\`\`

---

## Business Rules (Frontend Validation)

Frontend should validate these rules before submitting:

1. **Rule 1:** [Description]
   - Frontend validation: [How to validate client-side]
   - Example: "Budget must be greater than 0" → `value > 0`

2. **Rule 2:** [Description]
   - Frontend validation: [How to validate client-side]
   - Example: "End date must be after start date" → `endDate > startDate`

3. **Rule 3:** [Description]
   - Frontend validation: [How to validate client-side]

**Note:** Backend will still validate all rules. Frontend validation is for user experience only.

---

## State Management Recommendations

### Redux Slice Structure:

\`\`\`typescript
// src/features/[entities]/store/[entity]Slice.ts

interface [Entity]State {
  items: [Entity]Dto[];
  currentItem: [Entity]Dto | null;
  loading: boolean;
  error: string | null;
  pagination: {
    page: number;
    pageSize: number;
    totalCount: number;
  };
}
\`\`\`

### React Query Hooks:

\`\`\`typescript
// src/features/[entities]/api/use[Entity].ts

export const useGet[Entities] = () => {
  return useQuery({
    queryKey: ['[entities]'],
    queryFn: () => api.get('/api/[entities]')
  });
};

export const useCreate[Entity] = () => {
  return useMutation({
    mutationFn: (data: Create[Entity]Request) =>
      api.post('/api/[entities]', data)
  });
};
\`\`\`

---

## Error Handling

### Standard Error Response:

\`\`\`typescript
interface ErrorResponse {
  success: false;
  message: string;
  errorCode: string;
}
\`\`\`

### Common Error Codes:

| Code | Status | Description | Frontend Action |
|------|--------|-------------|-----------------|
| VALIDATION_ERROR | 400 | Invalid input data | Show field-level errors |
| NOT_FOUND | 404 | [Entity] not found | Show "not found" message |
| UNAUTHORIZED | 401 | No valid token | Redirect to login |
| FORBIDDEN | 403 | Missing permission | Show "access denied" |
| CONFLICT | 409 | Duplicate/constraint violation | Show specific conflict message |
| SERVER_ERROR | 500 | Unexpected error | Show generic error, log to monitoring |

### Error Handling Example:

\`\`\`typescript
try {
  const response = await api.post('/api/[entities]', data);
  // Handle success
} catch (error) {
  if (error.response?.status === 400) {
    // Show validation errors to user
    setErrors(error.response.data.message);
  } else if (error.response?.status === 401) {
    // Redirect to login
    navigate('/login');
  } else {
    // Generic error
    toast.error('Failed to create [entity]');
  }
}
\`\`\`

---

## Testing Recommendations

### Integration Testing:

The backend has E2E tests at:
\`\`\`
HammondPoleInc.Frontend/tests/e2e/[entity]-api.spec.ts
\`\`\`

These tests validate all endpoints. Frontend tests should:
- Mock API responses using these contracts
- Test UI behavior with various API states (loading, success, error)
- Test edge cases (empty lists, validation errors, etc.)

### Example API Mock:

\`\`\`typescript
// In your Vitest tests
vi.mock('../api/[entity]Api', () => ({
  get[Entities]: vi.fn(() => Promise.resolve({
    success: true,
    data: {
      items: [
        { id: '123', name: 'Test [Entity]', /* ... */ }
      ],
      totalCount: 1,
      page: 1,
      pageSize: 20
    }
  }))
}));
\`\`\`

---

## Example Integration

### Complete CRUD Example:

\`\`\`typescript
import { useState } from 'react';
import { useGet[Entities], useCreate[Entity], useUpdate[Entity], useDelete[Entity] } from './api/use[Entity]';

export function [Entity]Management() {
  const { data: [entities], isLoading } = useGet[Entities]();
  const create[Entity] = useCreate[Entity]();
  const update[Entity] = useUpdate[Entity]();
  const delete[Entity] = useDelete[Entity]();

  const handleCreate = async (formData: Create[Entity]Request) => {
    try {
      await create[Entity].mutateAsync(formData);
      toast.success('[Entity] created successfully');
    } catch (error) {
      toast.error('Failed to create [entity]');
    }
  };

  if (isLoading) return <Loading />;

  return (
    <div>
      {/* List, Create, Edit, Delete UI */}
    </div>
  );
}
\`\`\`

---

## Important Notes

### Date Handling:

- Backend returns: ISO 8601 strings (`"2025-11-18T14:30:00Z"`)
- Frontend should: Parse with `new Date(dateString)` or date library
- Display: Format for user's locale/timezone

### GUID Handling:

- Backend uses: GUIDs for entity IDs (`"123e4567-e89b-12d3-a456-426614174000"`)
- Frontend should: Treat as strings, don't parse

### Null vs Undefined:

- Backend returns: `null` for missing optional values
- Frontend should: Handle both `null` and `undefined` defensively

### Pagination:

- Page numbers are 1-based (first page = 1, not 0)
- Default page size: 20
- Available page sizes: 10, 20, 50

---

## Change Log

### Version History:

**v1.0 - [Date] - Initial Implementation**
- Created [Entity] aggregate with full CRUD
- Endpoints: [list]
- DTOs: [list]

**v1.1 - [Date] - Enhancement** (if ENHANCE mode ran)
- Added: [What was added]
- New endpoints: [list]
- Updated DTOs: [list]

**v1.1.1 - [Date] - Bug Fix** (if FIX mode ran)
- Fixed: [What bug was fixed]
- Behavior change: [Describe if API behavior changed]

---

## Frontend Implementation Checklist

Before building the frontend for this feature, ensure you have:

- [ ] Read this backend API report
- [ ] Understand all endpoints and their purposes
- [ ] Know the DTO structures (request and response)
- [ ] Understand authentication requirements
- [ ] Know the business rules to validate client-side
- [ ] Reviewed error handling patterns
- [ ] Checked example integration code

**Ready for Frontend?** Use the `react-feature-builder` skill with this report!

---

**Generated by:** DDD Feature Builder Agent
**For:** Frontend Integration
**Backend API Status:** Tested and Production Ready ✅
```

### What to Include in Report:

**Must Have:**
- ✅ All API endpoints (method, path, description)
- ✅ Complete request DTOs with TypeScript interfaces
- ✅ Complete response DTOs with TypeScript interfaces
- ✅ Authentication requirements (Bearer token, permissions)
- ✅ Business rules that frontend should validate
- ✅ Error codes and handling guidance
- ✅ Pagination details (if applicable)
- ✅ Example API calls with request/response

**Nice to Have:**
- ✅ State management recommendations (Redux/React Query)
- ✅ Testing guidance
- ✅ Common integration patterns
- ✅ Date/GUID handling notes
- ✅ Change log (for ENHANCE/FIX modes)

### When to Create/Update:

**CREATE Mode:**
- Create new report after validation phase passes
- Save to `ProjectArchitecture/FinalisedBackEndReports/[Entity]-Backend-API.md`

**ENHANCE Mode:**
- Open existing `[Entity]-Backend-API.md`
- Add new endpoints to endpoint table
- Add new properties to DTOs
- Update change log with v1.1, v1.2, etc.
- Save updated report

**FIX Mode:**
- Open existing `[Entity]-Backend-API.md` (if exists)
- Only update if API contract changed (response format, new error codes, etc.)
- Add bug fix to change log
- Most bug fixes won't need report updates (internal fixes)

---

## Example Interactions

### Example 1: CREATE Mode

**User:** "Create a new feature for Project Management"

**Skill:**
"What would you like to do?"
- Create a New Feature ← (User selects this)
- Enhance an Existing Feature
- Fix a Bug

"I'll help you create a complete Project Management feature following DDD patterns. Let me gather the requirements first."

[Asks all 13 questions from Phase 1A - CREATE]

[Scans for existing aggregates]

[Shows CREATE implementation plan]

"Does this plan look correct? Should I proceed with implementation?"

[User confirms]

[Implements all layers using TDD - write tests first!]

[Validates and cleans up]

[Provides CREATE mode final report]

---

### Example 2: ENHANCE Mode

**User:** "Enhance the Department feature to add budget tracking"

**Skill:**
"What would you like to do?"
- Create a New Feature
- Enhance an Existing Feature ← (User selects this)
- Fix a Bug

"I'll help you enhance the Department feature. Let me scan the existing implementation."

[Scans existing Department entity, service, endpoints]

[Shows current Department structure]

"I found the Department feature. Here's what exists:
- Properties: Id, Name, Code, Status, CreatedDate
- Operations: Create, Update, Delete, GetById, GetAll
- Endpoints: /api/departments (full CRUD)"

[Asks enhancement questions from Phase 1B - ENHANCE]

[Shows ENHANCE implementation plan with backward compatibility checks]

"This enhancement will add budget tracking while maintaining backward compatibility. Proceed?"

[User confirms]

[Writes tests for new functionality first (TDD)]

[Updates existing files with new properties/methods]

[Creates migration for new database columns]

[Validates - ensures all existing tests still pass!]

[Provides ENHANCE mode final report]

---

### Example 3: FIX Mode

**User:** "Fix bug in User feature - duplicate email returns 500 instead of 400"

**Skill:**
"What would you like to do?"
- Create a New Feature
- Enhance an Existing Feature
- Fix a Bug ← (User selects this)

"I'll help you fix this bug. Let me gather some details."

[Asks bug details from Phase 1C - FIX]

**Bug Analysis:**
Based on the symptoms ("returns 500 instead of 400"), I've identified:
- Affected Layer: Service Layer
- Root Cause: Missing existence check before creating user
- The service is likely throwing an unhandled DbException instead of BusinessException

**Fix Plan:**
Files to modify:
- `UserService.cs` - Add email existence check in CreateUserAsync
- `UserServiceTests.cs` - Add test for duplicate email scenario

**Show this analysis to user:** "Does this look correct?"

[User confirms]

[Writes failing test that reproduces bug (TDD RED)]

[Adds missing email check in service (TDD GREEN)]

[Runs all tests - ensure no regression]

[Provides FIX mode final report]

---

## References

- DDD Pattern Guide: `ProjectArchitecture\DDD-Pattern-Guide.md`
- Example Entity: `HammondPoleInc.Domain\Entities\Configuration\ApplicationUser.cs`
- Example Repository: `HammondPoleInc.Domain\Interfaces\Repositories\IUserRepository.cs`
- Example Service: `HammondPoleInc.Application\Services\UserService.cs`
- Example Endpoints: `HammondPoleInc.API\Endpoints\Configuration\UserEndpoints.cs`
