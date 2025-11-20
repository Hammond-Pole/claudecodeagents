# Feature Builders Plugin

Comprehensive feature builder skills for Hammond Pole Operations Platform - supporting full-stack feature development with Domain-Driven Design (DDD) and Test-Driven Development (TDD).

## 🎯 Purpose

This plugin provides two comprehensive skills that guide you through the complete lifecycle of feature development - from discovery to final reporting - with automatic planning, approval workflows, and comprehensive testing.

## 📦 What's Included

### Agents

#### 1. **ddd-feature-builder-agent** (13KB)
Autonomous agent that reads and executes the ddd-feature-builder skill file. Use this when you want the agent to autonomously handle the entire DDD feature lifecycle.

**Launch with Task tool:**
```
Use Task tool with subagent_type: ddd-feature-builder-agent
```

#### 2. **react-feature-builder-agent** (9KB)
Autonomous agent that reads and executes the react-feature-builder skill file. Use this when you want the agent to autonomously handle the entire React feature lifecycle.

**Launch with Task tool:**
```
Use Task tool with subagent_type: react-feature-builder-agent
```

### Skills

#### 1. **ddd-feature-builder** (60KB)
Build, enhance, and fix backend features following Domain-Driven Design patterns with comprehensive testing.

**Use for:**
- Creating new backend features (.NET 10)
- Enhancing existing backend functionality
- Fixing backend bugs
- Building DDD-compliant architectures

**Workflow:**
1. **Discovery Phase** - 13 comprehensive questions (CREATE mode)
2. **Plan Creation** - Saved to `ProjectArchitecture/Plans/`
3. **Plan Approval** - User must approve before coding
4. **TDD Implementation** - RED → GREEN → REFACTOR
5. **Database Migration** - Automatic migration generation
6. **Validation** - Build and test verification
7. **Cleanup** - Remove unused files
8. **Final Report** - Saved to `ProjectArchitecture/FinalisedBackEndReports/`

**Outputs:**
- Complete DDD layers: Domain → Infrastructure → Application → API → Contracts
- Unit tests with TDD discipline
- Database migrations
- Backend API report for frontend integration

---

#### 2. **react-feature-builder** (75KB)
Build, enhance, and fix React frontend features following established architectural patterns with comprehensive testing.

**Use for:**
- Building frontend UI (React 18 + TypeScript)
- Creating React components with RTK Query
- Enhancing existing UI functionality
- Fixing UI bugs
- Ensuring accessibility compliance

**Workflow:**
1. **Mode Detection** - CREATE / ENHANCE / FIX
2. **Discovery Phase** - 15 comprehensive questions including UI/UX details
3. **Backend Report Loading** - Loads existing backend API reports
4. **Plan Creation** - Saved to `ProjectArchitecture/Plans/`
5. **Plan Approval** - User must approve before coding
6. **TDD Implementation** - Component tests FIRST → Implementation → Refactor
7. **Accessibility Testing** - WCAG AA compliance verification
8. **Theme Testing** - Light + Dark mode validation
9. **Playwright Live Validation** - Manual UI testing with browser
10. **Final Report** - Saved to `ProjectArchitecture/FinalisedFrontEndReports/`

**Outputs:**
- Complete feature structure: api/, components/, types/, hooks/
- RTK Query integration with cache management
- Component unit tests (Vitest + Testing Library)
- E2E tests (Playwright)
- Accessible, themed, responsive UI
- Feature README

---

## 🚀 How to Use

### Installation

1. **Add the marketplace** to your project's Claude settings:
   ```json
   {
     "marketplaces": [
       "https://github.com/Hammond-Pole/claudecodeagents"
     ]
   }
   ```

2. **Install the plugin**:
   ```bash
   # In Claude Code, use the install command
   /install feature-builders
   ```

### Usage

#### Backend Feature Development

When the user says:
- "Create a new feature for [X]"
- "Implement [feature] following DDD"
- "Enhance [existing feature]"
- "Fix bug in [feature]"

**Invoke the skill:**
```
Use skill: feature-builders:ddd-feature-builder
```

The skill will automatically guide you through all phases.

#### Frontend Feature Development

When the user says:
- "Build frontend for [feature]"
- "Create UI for [feature]"
- "Enhance [feature] frontend"
- "Fix UI bug in [feature]"

**Invoke the skill:**
```
Use skill: feature-builders:react-feature-builder
```

The skill will automatically guide you through all phases.

---

## 🏗️ Architecture Alignment

### Backend (DDD)
- **Domain Layer**: Entities, Value Objects, Aggregates, Domain Services
- **Infrastructure Layer**: Repositories, Data Access, External Services
- **Application Layer**: DTOs, Services, Use Cases
- **API Layer**: Minimal API endpoints using `IEndpointDefinition` pattern
- **Contracts**: Request/Response DTOs

### Frontend (React)
- **Feature-Based Structure**: `src/features/[feature-name]/`
- **API Integration**: RTK Query with automatic cache invalidation
- **State Management**: Server state (RTK Query) + URL state (filters/pagination)
- **UI Components**: shadcn/ui + Radix UI primitives
- **Testing**: Vitest (unit) + Playwright (E2E)
- **Accessibility**: WCAG AA compliance + axe DevTools

---

## 📋 Key Features

### Test-Driven Development (TDD)
- ✅ **Tests written FIRST** before implementation
- ✅ RED → GREEN → REFACTOR cycle enforced
- ✅ High test coverage requirements
- ✅ Both unit and E2E tests

### Comprehensive Planning
- ✅ Discovery questions capture all requirements
- ✅ Implementation plans saved for audit trail
- ✅ User approval required before coding
- ✅ Plans show file structure and component hierarchy

### Validation & Quality
- ✅ Build verification (dotnet build / npm run build)
- ✅ Test verification (all tests must pass)
- ✅ Accessibility testing (WCAG AA)
- ✅ Theme testing (light + dark modes)
- ✅ Playwright live UI validation
- ✅ Unused file cleanup

### Documentation
- ✅ Backend API reports for frontend integration
- ✅ Frontend feature READMEs
- ✅ Implementation plans as audit trail
- ✅ TypeScript interfaces from backend DTOs

---

## 🔧 Technology Stack

### Backend
- .NET 10 (API) / .NET 9.0 (Aspire)
- Entity Framework Core with PostgreSQL
- ASP.NET Identity + Entra ID
- Minimal API architecture
- MSTest for unit testing

### Frontend
- React 18 + TypeScript
- Vite build tool
- Redux Toolkit + RTK Query
- MSAL for authentication
- TailwindCSS + Radix UI
- Vitest + Testing Library (unit tests)
- Playwright (E2E tests)

---

## 📊 Success Metrics

### Backend
- ✅ All DDD layers created
- ✅ Unit tests pass with > 90% coverage
- ✅ Build succeeds with 0 errors
- ✅ Database migration created and tested
- ✅ Backend API report generated

### Frontend
- ✅ All component tests pass
- ✅ All E2E tests pass
- ✅ Build succeeds with 0 errors
- ✅ No accessibility violations (WCAG AA)
- ✅ Works in light and dark themes
- ✅ Responsive on mobile/tablet/desktop
- ✅ Playwright live validation passed

---

## 🤝 Contributing

To enhance or modify these skills:

1. Update the skill markdown files in `skills/`
2. Test thoroughly in a real project
3. Update the version in `marketplace.json`
4. Commit and push to the repository

---

## 📝 License

MIT License - See [LICENSE](../../LICENSE) for details

---

## 👨‍💻 Author

**Hammond Pole Inc**
- Repository: https://github.com/Hammond-Pole/claudecodeagents
- Original Fork: https://github.com/wshobson/agents

---

## 🔗 Related Plugins

For complementary functionality, consider:
- **tdd-workflows** - TDD orchestration and testing patterns
- **backend-development** - General backend architecture patterns
- **frontend-mobile-development** - React and mobile development

---

## 📚 Additional Resources

- **DDD Pattern Guide**: `ProjectArchitecture/DDD-Pattern-Guide.md`
- **Frontend Pattern Guide**: `ProjectArchitecture/Frontend-Pattern-Guide.md`
- **Complete Feature Builder System**: `ProjectArchitecture/Complete-Feature-Builder-System.md`
