# DDD Feature Builder Skill

Build, enhance, and fix backend features following Domain-Driven Design patterns with Test-Driven Development.

## 📦 What's Included

### SKILL.md (60KB)
The main skill file that guides you through the complete DDD feature development lifecycle.

### DDD-Pattern-Guide.md (Bundled Context)
Comprehensive guide to the Hammond Pole Operations Platform's DDD architecture patterns. This file provides:
- Domain layer patterns (Entities, Value Objects, Aggregates)
- Infrastructure layer patterns (Repositories, Factories)
- Application layer patterns (Services, DTOs)
- API layer patterns (Minimal API with `IEndpointDefinition`)
- Database design patterns
- Testing patterns

**Note:** If your project already has `ProjectArchitecture/DDD-Pattern-Guide.md`, the skill will use that version. Otherwise, it will copy this bundled version to your project.

## 🎯 How It Works

### Phase -1: Project Architecture Setup
The skill automatically:
1. Checks if `ProjectArchitecture/` exists in your project
2. Creates the directory structure if needed:
   - `ProjectArchitecture/Plans/` - Implementation plans
   - `ProjectArchitecture/FinalisedBackEndReports/` - Backend API reports
   - `ProjectArchitecture/FinalisedFrontEndReports/` - Frontend reports
3. Copies the bundled DDD-Pattern-Guide.md if not present
4. Verifies the structure is ready

### Complete Workflow
1. **Discovery** - 13 comprehensive questions (CREATE mode)
2. **Plan Creation** - Saved to `ProjectArchitecture/Plans/`
3. **Plan Approval** - User must approve before coding
4. **TDD Implementation** - RED → GREEN → REFACTOR
5. **Database Migration** - Automatic generation
6. **Validation** - Build and test verification
7. **Cleanup** - Remove unused files
8. **Final Report** - Saved to `ProjectArchitecture/FinalisedBackEndReports/`

## 🏗️ Output Structure

### DDD Layers Created
- **Domain**: Entities, Value Objects, Domain Services, Domain Events
- **Infrastructure**: Repositories, Factories, External Service Integrations
- **Application**: Application Services, DTOs, Use Cases
- **API**: Minimal API endpoints with `IEndpointDefinition` pattern
- **Contracts**: Request/Response DTOs for API

### Additional Outputs
- Unit tests (MSTest)
- Database migration (EF Core)
- Backend API report for frontend integration

## 📝 Usage

Invoke with:
```
Use skill: feature-builders:ddd-feature-builder
```

Or in CLAUDE.md:
```markdown
**When user requests:**
- "Create a new feature for [X]"
- "Implement [feature] following DDD"

**Action:** Invoke feature-builders:ddd-feature-builder
```

## 🔗 Dependencies

- **.NET 10** - Primary API framework
- **.NET 9.0** - Aspire orchestration
- **Entity Framework Core** - Data access
- **PostgreSQL** - Database
- **MSTest** - Testing framework

## 📚 Related Skills

- **react-feature-builder** - Frontend UI for backend features
- **tdd-workflows** - TDD orchestration patterns

## 🤝 Contributing

To update this skill:
1. Edit `SKILL.md` for workflow changes
2. Edit `DDD-Pattern-Guide.md` for architecture pattern updates
3. Test thoroughly in a real project
4. Update version in marketplace.json

## 📄 License

MIT License - See plugin LICENSE for details
