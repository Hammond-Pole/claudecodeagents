# Frontend Architecture Pattern Guide - Hammond Pole Operations Platform

## Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [Project Structure](#project-structure)
3. [State Management](#state-management)
4. [Component Patterns](#component-patterns)
5. [API Integration](#api-integration)
6. [Styling & Theming](#styling--theming)
7. [Routing & Navigation](#routing--navigation)
8. [Forms & Validation](#forms--validation)
9. [Performance Optimization](#performance-optimization)
10. [Testing Patterns](#testing-patterns)
11. [Best Practices](#best-practices)
12. [Common Pitfalls](#common-pitfalls)

---

## Architecture Overview

### Technology Stack

**Core Framework:**
- **React 18** - Component library with concurrent features
- **TypeScript** - Type-safe JavaScript (strict mode enabled)
- **Vite** - Build tool with fast HMR

**State Management:**
- **Redux Toolkit** - Global UI state (auth, dashboard)
- **RTK Query** - Server state with caching (built on Redux Toolkit)
- **React Query/Tanstack Query** - Alternative server state (if needed)

**UI & Styling:**
- **shadcn/ui** - Component library built on Radix UI primitives
- **TailwindCSS** - Utility-first CSS framework
- **Radix UI** - Unstyled accessible components
- **Lucide React** - Icon library
- **next-themes** - Theme management (light/dark mode)

**Routing & Forms:**
- **React Router v6** - Client-side routing
- **React Hook Form** - Performant forms with validation
- **Zod** - TypeScript-first schema validation

**Authentication:**
- **MSAL React** - Microsoft Entra ID (Azure AD) authentication

**Testing:**
- **Vitest** - Unit testing framework
- **React Testing Library** - Component testing utilities
- **Playwright** - End-to-end testing
- **jest-axe** - Accessibility testing

---

## Project Structure

### Folder Organization

```
HammondPoleInc.Frontend/
├── src/
│   ├── features/                 # Feature-based modules (PRIMARY ORGANIZATION)
│   │   ├── authentication/
│   │   │   ├── components/       # Auth-specific components
│   │   │   ├── hooks/            # useAuth custom hook
│   │   │   ├── store/            # authSlice (Redux)
│   │   │   ├── services/         # entraIdService (MSAL)
│   │   │   └── types/            # Auth TypeScript types
│   │   │
│   │   ├── dashboard/
│   │   │   ├── components/       # Dashboard components
│   │   │   ├── store/            # dashboardSlice (Redux)
│   │   │   └── types/            # Dashboard types
│   │   │
│   │   └── configuration/
│   │       └── users/
│   │           ├── api/          # RTK Query endpoints
│   │           │   └── userApi.ts
│   │           ├── components/   # Feature components
│   │           │   ├── UserManagementComplete.tsx
│   │           │   ├── EditUserModal.tsx
│   │           │   ├── UserCardView.tsx
│   │           │   └── organogram/
│   │           ├── hooks/        # Feature hooks
│   │           └── types/        # Feature types
│   │
│   ├── components/               # Shared components
│   │   ├── ui/                   # shadcn/ui components
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── select.tsx
│   │   │   └── ...
│   │   ├── layout/               # Layout components
│   │   │   ├── MainLayout.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Header.tsx
│   │   ├── ProtectedRoute.tsx
│   │   ├── LoadingFallback.tsx
│   │   └── ErrorBoundary.tsx
│   │
│   ├── store/                    # Redux store configuration
│   │   ├── index.ts              # Store setup
│   │   └── api.ts                # RTK Query base API
│   │
│   ├── config/                   # Configuration files
│   │   ├── api.ts                # API endpoints configuration
│   │   ├── routes.ts             # Route constants
│   │   └── msalConfig.ts         # MSAL authentication config
│   │
│   ├── hooks/                    # Shared custom hooks
│   │   ├── useQueryParams.ts     # URL state management
│   │   ├── useApiError.ts        # Error handling
│   │   └── useDebounce.ts        # Debounced values
│   │
│   ├── lib/                      # Utility libraries
│   │   └── utils.ts              # cn() helper
│   │
│   ├── services/                 # Global services
│   │   ├── signalrService.ts     # Real-time updates
│   │   └── notificationService.ts
│   │
│   ├── theme/                    # Theme configuration
│   │   └── theme-provider.tsx
│   │
│   ├── types/                    # Shared TypeScript types
│   │   ├── api.types.ts          # API response types
│   │   └── common.types.ts       # Common interfaces
│   │
│   ├── utils/                    # Utility functions
│   │   └── apiErrorHandler.ts
│   │
│   └── test/                     # Test utilities
│       └── setup.ts
│
├── tests/
│   └── e2e/                      # Playwright E2E tests
│       ├── user-api.spec.ts
│       └── task-api.spec.ts
│
└── public/                       # Static assets
    └── vite.svg
```

---

## State Management

### Three-Layer State Model

```
┌─────────────────────────────────────────────────┐
│         Layer 1: Server State                   │
│    (RTK Query - API data, caching, sync)        │
│  useGetUsersQuery, useUpdateUserMutation        │
└─────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────┐
│       Layer 2: Global UI State                  │
│   (Redux Toolkit - Auth, Dashboard, Theme)      │
│    authSlice, dashboardSlice                    │
└─────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────┐
│       Layer 3: Local Component State            │
│  (useState, useReducer - Forms, Modals, UI)     │
│   const [isOpen, setIsOpen] = useState(false)   │
└─────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────┐
│           Layer 4: URL State                    │
│      (useQueryParams - Shareable state)         │
│   page, search, filters in URL params           │
└─────────────────────────────────────────────────┘
```

### When to Use Each Layer

| State Type | Layer | Example |
|------------|-------|---------|
| API data, user lists, task data | Server State (RTK Query) | `useGetUsersQuery()` |
| Authentication, current user | Global UI State (Redux) | `authSlice` |
| Real-time dashboard data | Global UI State (Redux) | `dashboardSlice` |
| Modal open/closed | Local State (useState) | `const [isOpen, setIsOpen]` |
| Form inputs | Local State (useState) | `const [formData, setFormData]` |
| Pagination, filters, search | URL State (useQueryParams) | `params.page`, `params.search` |

---

## Component Patterns

### Pattern 1: Feature Page Component

**Purpose:** Top-level component that orchestrates a feature

```typescript
// features/configuration/users/components/UserManagementComplete.tsx
import React, { lazy, Suspense, useMemo, useCallback, useDeferredValue } from 'react'

export const UserManagementComplete: React.FC = () => {
  // ============================================================================
  // URL STATE (shareable, bookmarkable)
  // ============================================================================
  const { params, setParam, setParams } = useQueryParams({
    tab: { type: 'string', default: 'list' },
    page: { type: 'number', default: 1 },
    pageSize: { type: 'number', default: 20 },
    viewMode: { type: 'string', default: 'table' },
    search: { type: 'string', default: '' },
    roleFilter: { type: 'string', default: '' },
    typeFilter: { type: 'string', default: '' }
  })

  // ============================================================================
  // PERFORMANCE: Deferred search (smooth UX during typing)
  // ============================================================================
  const deferredSearch = useDeferredValue(params.search)

  // ============================================================================
  // DATA FETCHING (RTK Query)
  // ============================================================================
  const {
    data: managementData,
    isLoading,
    isFetching,
    error,
    refetch
  } = useGetUserManagementDataQuery(queryParams, {
    skip: params.tab !== 'list' // Conditional fetching
  })

  // ============================================================================
  // LOCAL STATE (modals, selections)
  // ============================================================================
  const [selectedUserId, setSelectedUserId] = useState<string | null>(null)
  const [isEditModalOpen, setIsEditModalOpen] = useState(false)

  // ============================================================================
  // OPTIMIZED HANDLERS (useCallback)
  // ============================================================================
  const handleEditUser = useCallback((userId: string) => {
    setSelectedUserId(userId)
    setIsEditModalOpen(true)
  }, [])

  const handlePageChange = useCallback((newPage: number) => {
    setParam('page', newPage)
  }, [setParam])

  // ============================================================================
  // LOADING & ERROR STATES
  // ============================================================================
  if (isLoading) return <LoadingSpinner />
  if (error) return <ErrorDisplay error={error} onRetry={refetch} />

  // ============================================================================
  // RENDER
  // ============================================================================
  return (
    <div className="min-h-screen bg-background p-6">
      {/* Search & Filters */}
      <div className="mb-4 flex gap-4">
        <Input
          placeholder="Search users..."
          value={params.search}
          onChange={(e) => setParam('search', e.target.value)}
        />
      </div>

      {/* Data Display */}
      {params.viewMode === 'table' ? (
        <UserTableView users={managementData?.users || []} />
      ) : (
        <UserCardView users={managementData?.users || []} />
      )}

      {/* Lazy-loaded Modal */}
      <Suspense fallback={null}>
        {isEditModalOpen && (
          <EditUserModal
            isOpen={isEditModalOpen}
            onClose={() => setIsEditModalOpen(false)}
            userId={selectedUserId}
          />
        )}
      </Suspense>
    </div>
  )
}
```

### Pattern 2: Modal Component

```typescript
// features/configuration/users/components/EditUserModal.tsx
import { Dialog, DialogContent, DialogHeader, DialogTitle } from '@/components/ui/dialog'

interface EditUserModalProps {
  isOpen: boolean
  onClose: () => void
  userId: string
}

export const EditUserModal: React.FC<EditUserModalProps> = ({ isOpen, onClose, userId }) => {
  const [formData, setFormData] = useState<UpdateUserRequest>({
    applicationUserTypeId: undefined,
    applicationUserRoleId: undefined,
    parentUserId: undefined,
    departmentId: undefined
  })

  const [updateUser, { isLoading }] = useUpdateUserMutation()

  const handleSave = async () => {
    try {
      await updateUser({ userId, request: formData }).unwrap()
      toast.success('User updated successfully')
      onClose()
    } catch (error: any) {
      const parsedError = parseApiError(error)
      toast.error(parsedError.message)
    }
  }

  return (
    <Dialog open={isOpen} onOpenChange={onClose}>
      <DialogContent className="max-w-3xl">
        <DialogHeader>
          <DialogTitle>Edit User</DialogTitle>
        </DialogHeader>

        {/* Form Fields */}
        <div className="space-y-4">
          <div>
            <Label htmlFor="userType">User Type</Label>
            <Select
              value={formData.applicationUserTypeId?.toString() || ""}
              onValueChange={(value) => setFormData(prev => ({
                ...prev,
                applicationUserTypeId: parseInt(value)
              }))}
            >
              <SelectTrigger>
                <SelectValue placeholder="Select Type" />
              </SelectTrigger>
              <SelectContent>
                {userTypes.map(type => (
                  <SelectItem key={type.id} value={type.id.toString()}>
                    {type.displayName}
                  </SelectItem>
                ))}
              </SelectContent>
            </Select>
          </div>

          <div className="flex justify-end gap-2 mt-6">
            <Button variant="outline" onClick={onClose} disabled={isLoading}>
              Cancel
            </Button>
            <Button onClick={handleSave} disabled={isLoading}>
              {isLoading ? 'Saving...' : 'Save'}
            </Button>
          </div>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

### Pattern 3: Reusable Data Display Component

```typescript
// components/ui/data-table.tsx
interface DataTableProps<T> {
  columns: ColumnDef<T>[]
  data: T[]
  isLoading?: boolean
  pagination?: {
    page: number
    pageSize: number
    totalCount: number
    onPageChange: (page: number) => void
  }
}

export function DataTable<T>({ columns, data, isLoading, pagination }: DataTableProps<T>) {
  return (
    <div className="rounded-md border">
      <Table>
        <TableHeader>
          <TableRow>
            {columns.map((column) => (
              <TableHead key={column.id}>{column.header}</TableHead>
            ))}
          </TableRow>
        </TableHeader>
        <TableBody>
          {isLoading ? (
            <TableRow>
              <TableCell colSpan={columns.length} className="text-center">
                Loading...
              </TableCell>
            </TableRow>
          ) : data.length === 0 ? (
            <TableRow>
              <TableCell colSpan={columns.length} className="text-center">
                No results found
              </TableCell>
            </TableRow>
          ) : (
            data.map((row, i) => (
              <TableRow key={i}>
                {columns.map((column) => (
                  <TableCell key={column.id}>
                    {column.cell(row)}
                  </TableCell>
                ))}
              </TableRow>
            ))
          )}
        </TableBody>
      </Table>

      {pagination && (
        <div className="flex items-center justify-between px-4 py-4">
          <div className="text-sm text-muted-foreground">
            Showing {((pagination.page - 1) * pagination.pageSize) + 1} to{' '}
            {Math.min(pagination.page * pagination.pageSize, pagination.totalCount)} of{' '}
            {pagination.totalCount} results
          </div>
          <div className="flex gap-2">
            <Button
              variant="outline"
              size="sm"
              onClick={() => pagination.onPageChange(pagination.page - 1)}
              disabled={pagination.page === 1}
            >
              Previous
            </Button>
            <Button
              variant="outline"
              size="sm"
              onClick={() => pagination.onPageChange(pagination.page + 1)}
              disabled={pagination.page * pagination.pageSize >= pagination.totalCount}
            >
              Next
            </Button>
          </div>
        </div>
      )}
    </div>
  )
}
```

---

## API Integration

### RTK Query Endpoint Injection

```typescript
// features/tasks/api/taskApi.ts
import { api } from '@/store/api'
import { apiEndpoints } from '@/config/api'
import type { TaskDto, CreateTaskRequest, TaskPaginationQuery } from '@/types/task.types'

export const taskApi = api.injectEndpoints({
  endpoints: (builder) => ({
    // QUERY - GET requests
    getTasks: builder.query<PagedResult<TaskDto>, TaskPaginationQuery>({
      query: (params) => {
        const searchParams = new URLSearchParams()
        if (params.page) searchParams.append('page', params.page.toString())
        if (params.pageSize) searchParams.append('pageSize', params.pageSize.toString())
        if (params.search) searchParams.append('search', params.search)
        if (params.statusFilter) searchParams.append('statusFilter', params.statusFilter)
        if (params.priorityFilter) searchParams.append('priorityFilter', params.priorityFilter)

        return `/tasks?${searchParams.toString()}`
      },
      transformResponse: (response: StructuredResponse<PagedResult<TaskDto>>) => {
        return response.data!
      },
      providesTags: (result) =>
        result
          ? [
              ...result.items.map(({ id }) => ({ type: 'Tasks' as const, id })),
              { type: 'Tasks', id: 'LIST' }
            ]
          : [{ type: 'Tasks', id: 'LIST' }],
    }),

    getTask: builder.query<TaskDto, string>({
      query: (id) => `/tasks/${id}`,
      transformResponse: (response: StructuredResponse<TaskDto>) => response.data!,
      providesTags: (result, error, id) => [{ type: 'Tasks', id }],
    }),

    // MUTATION - POST/PUT/DELETE requests
    createTask: builder.mutation<TaskDto, CreateTaskRequest>({
      query: (request) => ({
        url: '/tasks',
        method: 'POST',
        body: request
      }),
      transformResponse: (response: StructuredResponse<TaskDto>) => response.data!,
      invalidatesTags: [{ type: 'Tasks', id: 'LIST' }], // Refetch list
    }),

    assignTask: builder.mutation<TaskDto, { taskId: string; userId: string }>({
      query: ({ taskId, userId }) => ({
        url: `/tasks/${taskId}/assign`,
        method: 'PUT',
        body: { assignedToUserId: userId }
      }),
      transformResponse: (response: StructuredResponse<TaskDto>) => response.data!,
      invalidatesTags: (result, error, { taskId }) => [
        { type: 'Tasks', id: taskId },
        { type: 'Tasks', id: 'LIST' }
      ],
    }),

    completeTask: builder.mutation<TaskDto, string>({
      query: (taskId) => ({
        url: `/tasks/${taskId}/complete`,
        method: 'PUT'
      }),
      transformResponse: (response: StructuredResponse<TaskDto>) => response.data!,
      invalidatesTags: (result, error, taskId) => [
        { type: 'Tasks', id: taskId },
        { type: 'Tasks', id: 'LIST' }
      ],
      onQueryStarted: async (taskId, { dispatch, queryFulfilled }) => {
        // Optimistic update
        const patchResult = dispatch(
          taskApi.util.updateQueryData('getTask', taskId, (draft) => {
            draft.status = 'Completed'
            draft.completedDate = new Date().toISOString()
          })
        )

        try {
          await queryFulfilled
        } catch {
          patchResult.undo() // Rollback on error
        }
      }
    })
  })
})

// Export generated hooks
export const {
  useGetTasksQuery,
  useGetTaskQuery,
  useCreateTaskMutation,
  useAssignTaskMutation,
  useCompleteTaskMutation
} = taskApi
```

### Key Patterns:

✅ **Endpoint Injection** - Feature APIs inject into base API
✅ **Cache Tags** - Automatic cache invalidation
✅ **Transform Response** - Unwrap StructuredResponse
✅ **Optimistic Updates** - Update UI before server responds
✅ **Generated Hooks** - Auto-generated from endpoints

---

## Styling & Theming

### Tailwind + Component Variants

```typescript
// components/ui/badge.tsx
import { cva, type VariantProps } from "class-variance-authority"

const badgeVariants = cva(
  "inline-flex items-center rounded-md border px-2.5 py-0.5 text-xs font-semibold transition-colors",
  {
    variants: {
      variant: {
        default: "border-transparent bg-primary text-primary-foreground",
        secondary: "border-transparent bg-secondary text-secondary-foreground",
        destructive: "border-transparent bg-destructive text-destructive-foreground",
        outline: "text-foreground",
        success: "border-transparent bg-green-500 text-white",
        warning: "border-transparent bg-yellow-500 text-white",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

export const Badge: React.FC<VariantProps<typeof badgeVariants> & Props> = ({
  className,
  variant,
  ...props
}) => {
  return <div className={cn(badgeVariants({ variant }), className)} {...props} />
}

// Usage
<Badge variant="success">Active</Badge>
<Badge variant="destructive">Inactive</Badge>
```

### Theme Configuration

```typescript
// tailwind.config.js
module.exports = {
  darkMode: ["class"],
  content: ["./src/**/*.{ts,tsx}"],
  theme: {
    extend: {
      colors: {
        // Hammond Pole branding
        navy: '#333756',
        golden: '#FBB03C',

        // Theme system
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}
```

---

## Routing & Navigation

### Route Configuration

```typescript
// App.tsx
const router = createBrowserRouter([
  {
    path: '/login',
    element: <LoginPage />
  },
  {
    path: '/',
    element: <ProtectedRoute><MainLayout /></ProtectedRoute>,
    children: [
      { index: true, element: <Navigate to="/dashboard" replace /> },
      { path: "dashboard", element: withSuspense(Dashboard) },
      { path: "users", element: withSuspense(UserManagement) },
      { path: "config/departments", element: withSuspense(Departments) }
    ]
  },
  {
    path: "*",
    element: <Navigate to="/dashboard" replace />
  }
])
```

### Navigation Hook

```typescript
import { useNavigate } from 'react-router-dom'

function MyComponent() {
  const navigate = useNavigate()

  const handleClick = () => {
    navigate('/users', { state: { fromDashboard: true } })
  }

  return <Button onClick={handleClick}>View Users</Button>
}
```

---

## Forms & Validation

### React Hook Form + Zod Pattern

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import * as z from 'zod'

const taskSchema = z.object({
  title: z.string().min(1, 'Title is required').max(200, 'Title too long'),
  description: z.string().max(2000, 'Description too long').optional(),
  dueDate: z.string().datetime().refine(
    (date) => new Date(date) > new Date(),
    'Due date must be in the future'
  ).optional(),
  priority: z.enum(['Low', 'Medium', 'High', 'Urgent']).default('Medium'),
  assignedToUserId: z.string().uuid().optional(),
  departmentId: z.number().int().positive().optional()
})

type TaskFormData = z.infer<typeof taskSchema>

function CreateTaskForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    watch,
    setValue
  } = useForm<TaskFormData>({
    resolver: zodResolver(taskSchema),
    defaultValues: {
      title: '',
      description: '',
      priority: 'Medium'
    }
  })

  const [createTask] = useCreateTaskMutation()

  const onSubmit = async (data: TaskFormData) => {
    try {
      await createTask(data).unwrap()
      toast.success('Task created!')
    } catch (error) {
      const parsedError = parseApiError(error)
      toast.error(parsedError.message)
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <Label htmlFor="title">Title *</Label>
        <Input id="title" {...register('title')} />
        {errors.title && (
          <p className="text-sm text-destructive">{errors.title.message}</p>
        )}
      </div>

      <div>
        <Label htmlFor="description">Description</Label>
        <Textarea id="description" {...register('description')} rows={4} />
        {errors.description && (
          <p className="text-sm text-destructive">{errors.description.message}</p>
        )}
      </div>

      <div>
        <Label htmlFor="priority">Priority</Label>
        <Select
          value={watch('priority')}
          onValueChange={(value) => setValue('priority', value as any)}
        >
          <SelectTrigger>
            <SelectValue />
          </SelectTrigger>
          <SelectContent>
            <SelectItem value="Low">Low</SelectItem>
            <SelectItem value="Medium">Medium</SelectItem>
            <SelectItem value="High">High</SelectItem>
            <SelectItem value="Urgent">Urgent</SelectItem>
          </SelectContent>
        </Select>
      </div>

      <Button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Creating...' : 'Create Task'}
      </Button>
    </form>
  )
}
```

---

## Performance Optimization

### React 18 Concurrent Features

```typescript
// useDeferredValue for search inputs
const searchInput = params.search
const deferredSearch = useDeferredValue(searchInput)

// User types immediately, UI updates instantly
// But API query uses deferredSearch → debounced automatically

// useTransition for non-urgent updates
const [isPending, startTransition] = useTransition()

const handleTabChange = (newTab: string) => {
  startTransition(() => {
    setTab(newTab) // Non-urgent update
  })
}
```

### Memoization Patterns

```typescript
// useMemo for expensive computations
const filteredUsers = useMemo(() => {
  return users.filter(u =>
    u.name.toLowerCase().includes(deferredSearch.toLowerCase())
  )
}, [users, deferredSearch])

// useCallback for event handlers (prevent child re-renders)
const handleEditUser = useCallback((userId: string) => {
  setSelectedUserId(userId)
  setIsEditModalOpen(true)
}, []) // Empty deps = function never changes

// React.memo for pure components
export const UserCard = React.memo<UserCardProps>(({ user, onEdit }) => {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{user.displayName}</CardTitle>
      </CardHeader>
    </Card>
  )
}, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.user.id === nextProps.user.id
})
```

### Lazy Loading Strategy

```typescript
// Route-level code splitting
const Dashboard = lazy(() => import('./features/dashboard/components/Dashboard'))

// Modal lazy loading (loaded only when opened)
const HeavyModal = lazy(() => import('./components/HeavyModal'))

// Conditional rendering with Suspense
{isModalOpen && (
  <Suspense fallback={<ModalSkeleton />}>
    <HeavyModal />
  </Suspense>
)}
```

---

## Testing Patterns

### Unit Testing (Vitest + React Testing Library)

```typescript
// features/tasks/components/TaskCard.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { TaskCard } from './TaskCard'

describe('TaskCard', () => {
  const mockTask = {
    id: 'task-123',
    title: 'Test Task',
    status: 'InProgress',
    priority: 'High',
    isOverdue: false
  }

  const mockHandlers = {
    onComplete: vi.fn(),
    onEdit: vi.fn()
  }

  it('renders task information correctly', () => {
    render(<TaskCard task={mockTask} {...mockHandlers} />)

    expect(screen.getByText('Test Task')).toBeInTheDocument()
    expect(screen.getByText('High')).toBeInTheDocument()
    expect(screen.getByText('InProgress')).toBeInTheDocument()
  })

  it('calls onComplete when complete button clicked', async () => {
    const user = userEvent.setup()
    render(<TaskCard task={mockTask} {...mockHandlers} />)

    const completeButton = screen.getByRole('button', { name: /complete/i })
    await user.click(completeButton)

    await waitFor(() => {
      expect(mockHandlers.onComplete).toHaveBeenCalledWith('task-123')
    })
  })

  it('has no accessibility violations', async () => {
    const { container } = render(<TaskCard task={mockTask} {...mockHandlers} />)
    const results = await axe(container)
    expect(results).toHaveNoViolations()
  })
})
```

### E2E Testing (Playwright)

```typescript
// tests/e2e/task-management.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Task Management Flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('http://localhost:3100/login')
    await page.fill('[name="email"]', 'test@example.com')
    await page.fill('[name="password"]', 'password123')
    await page.click('button[type="submit"]')
    await page.waitForURL('**/dashboard')
  })

  test('should create, assign, and complete task', async ({ page }) => {
    // Navigate to tasks
    await page.goto('http://localhost:3100/tasks')

    // Create task
    await page.click('button:has-text("New Task")')
    await page.fill('[name="title"]', 'E2E Test Task')
    await page.fill('[name="description"]', 'Created by automated test')
    await page.selectOption('[name="priority"]', 'High')
    await page.click('button:has-text("Create")')

    // Verify task appears in list
    await expect(page.locator('text=E2E Test Task')).toBeVisible()

    // Assign task
    await page.click('text=E2E Test Task >> .. >> button:has-text("Assign")')
    await page.selectOption('[name="assignedTo"]', 'john.doe@example.com')
    await page.click('button:has-text("Save")')

    // Complete task
    await page.click('text=E2E Test Task >> .. >> button:has-text("Complete")')

    // Verify status changed
    await expect(page.locator('text=E2E Test Task >> .. >> text=Completed')).toBeVisible()
  })
})
```

---

## Best Practices

### 1. TypeScript Best Practices

✅ **DO:**
- Use strict mode (`strict: true` in tsconfig)
- Define interfaces for all props
- Type API responses
- Use discriminated unions for state
- Prefer `type` over `interface` for props (unless extending)

```typescript
// Good: Discriminated union
type LoadingState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: Task[] }
  | { status: 'error'; error: string }

// Good: Props with explicit types
type TaskCardProps = {
  task: TaskDto
  onComplete: (id: string) => void
  onEdit: (id: string) => void
  className?: string
}

// Good: Typed API response
interface ApiResponse<T> {
  success: boolean
  data: T | null
  message: string
}
```

❌ **DON'T:**
- Use `any` (use `unknown` if type is truly unknown)
- Skip prop type definitions
- Use `as` for type assertions (prefer type guards)

### 2. Component Best Practices

✅ **DO:**
- Keep components focused (single responsibility)
- Extract reusable logic to custom hooks
- Use `React.FC` or explicit return types
- Destructure props in function signature
- Group related state with `useReducer` (complex state)

```typescript
// Good: Focused component
export const TaskCard: React.FC<TaskCardProps> = ({
  task,
  onComplete,
  onEdit,
  className
}) => {
  const statusColor = task.status === 'Completed' ? 'success' : 'default'

  return (
    <Card className={className}>
      <CardHeader>
        <CardTitle>{task.title}</CardTitle>
        <Badge variant={statusColor}>{task.status}</Badge>
      </CardHeader>
      <CardContent>{task.description}</CardContent>
      <CardFooter>
        {task.status !== 'Completed' && (
          <Button onClick={() => onComplete(task.id)}>Complete</Button>
        )}
      </CardFooter>
    </Card>
  )
}
```

❌ **DON'T:**
- Create god components (>500 lines)
- Mix API logic with UI logic
- Forget loading/error states
- Skip accessibility attributes

### 3. State Management Best Practices

✅ **DO:**
- Use RTK Query for all API data
- Use Redux slices only for cross-feature UI state
- Use URL params for shareable state (pagination, filters)
- Keep local state minimal

```typescript
// Good: Server state with RTK Query
const { data: tasks } = useGetTasksQuery({ page: 1 })

// Good: Global UI state in Redux
const { user, isAuthenticated } = useSelector((state: RootState) => state.auth)

// Good: URL params for filters
const { params, setParam } = useQueryParams({ page: { type: 'number', default: 1 } })

// Good: Local state for UI
const [isModalOpen, setIsModalOpen] = useState(false)
```

❌ **DON'T:**
- Store API data in Redux slices (use RTK Query)
- Duplicate data in multiple state sources
- Forget to invalidate cache tags after mutations

### 4. Performance Best Practices

✅ **DO:**
- Lazy load route components
- Use `useDeferredValue` for search inputs
- Memoize expensive computations
- Use `React.memo` for pure components
- Implement virtual scrolling for large lists (react-window)

```typescript
// Good: Lazy loaded routes
const Dashboard = lazy(() => import('./Dashboard'))

// Good: Deferred search
const deferredSearch = useDeferredValue(searchInput)

// Good: Memoized filtering
const filteredTasks = useMemo(() =>
  tasks.filter(t => t.title.includes(deferredSearch)),
  [tasks, deferredSearch]
)
```

❌ **DON'T:**
- Create inline objects/arrays in render (causes re-renders)
- Skip memoization for expensive operations
- Fetch data on every render

### 5. Error Handling Best Practices

✅ **DO:**
- Use `parseApiError` utility
- Show user-friendly messages
- Log errors to console (or monitoring service)
- Provide retry mechanisms

```typescript
try {
  await createTask(data).unwrap()
  toast.success('Task created!')
} catch (error) {
  const parsedError = parseApiError(error)
  toast.error(parsedError.message)
  console.error('Task creation failed:', parsedError)
}
```

---

## Common Pitfalls

### 1. State Management Anti-Patterns

❌ **WRONG:**
```typescript
// Storing API data in Redux slice
const usersSlice = createSlice({
  name: 'users',
  initialState: { users: [], loading: false },
  reducers: {
    setUsers: (state, action) => {
      state.users = action.payload // Manual cache management - BAD!
    }
  }
})
```

✅ **CORRECT:**
```typescript
// Use RTK Query for API data
export const userApi = api.injectEndpoints({
  endpoints: (builder) => ({
    getUsers: builder.query<User[], void>({
      query: () => '/users',
      providesTags: ['Users'] // Automatic cache management - GOOD!
    })
  })
})

const { data: users } = useGetUsersQuery()
```

### 2. Component Re-render Hell

❌ **WRONG:**
```typescript
// Creating new objects/functions on every render
function TaskList() {
  const handleClick = (id) => { /* ... */ } // New function every render!
  const filters = { status: 'active' } // New object every render!

  return (
    <TaskCard onEdit={handleClick} filters={filters} />
    // TaskCard re-renders unnecessarily
  )
}
```

✅ **CORRECT:**
```typescript
function TaskList() {
  const handleClick = useCallback((id) => { /* ... */ }, []) // Memoized
  const filters = useMemo(() => ({ status: 'active' }), []) // Memoized

  return <TaskCard onEdit={handleClick} filters={filters} />
}
```

### 3. Missing Loading/Error States

❌ **WRONG:**
```typescript
function TaskList() {
  const { data } = useGetTasksQuery()

  return (
    <div>
      {data.tasks.map(task => <TaskCard task={task} />)}
      {/* Crashes if data is undefined! */}
    </div>
  )
}
```

✅ **CORRECT:**
```typescript
function TaskList() {
  const { data, isLoading, error } = useGetTasksQuery()

  if (isLoading) return <LoadingSpinner />
  if (error) return <ErrorDisplay error={error} />
  if (!data?.tasks) return <EmptyState />

  return (
    <div>
      {data.tasks.map(task => <TaskCard key={task.id} task={task} />)}
    </div>
  )
}
```

### 4. Accessibility Violations

❌ **WRONG:**
```tsx
<div onClick={handleClick}>Click me</div> {/* Not keyboard accessible */}
<img src="..." /> {/* Missing alt text */}
<input /> {/* Missing label */}
```

✅ **CORRECT:**
```tsx
<Button onClick={handleClick}>Click me</Button> {/* Keyboard accessible */}
<img src="..." alt="User profile photo" /> {/* Alt text provided */}
<Label htmlFor="email">Email</Label>
<Input id="email" /> {/* Associated label */}
```

---

## Summary - Frontend Architecture Philosophy

### Core Principles:

1. **Feature-Based Organization** - Self-contained, scalable modules
2. **Type Safety First** - TypeScript strict mode, comprehensive typing
3. **Server State via RTK Query** - Automatic caching, refetching, optimistic updates
4. **Global UI State via Redux** - Authentication, cross-feature state only
5. **URL State for Filters** - Shareable, bookmarkable, deep-linkable
6. **Component Composition** - Small, focused, reusable components
7. **Accessibility by Default** - WCAG AA compliance, semantic HTML, ARIA
8. **Performance Optimized** - Lazy loading, memoization, code splitting
9. **Testability** - Unit, E2E, and accessibility tests
10. **Developer Experience** - Clear patterns, fast feedback, excellent tooling

### The Hammond Pole Frontend Way:

✅ **RTK Query** for all API data (never Redux slices)
✅ **useQueryParams** for shareable state (pagination, filters)
✅ **shadcn/ui** components with Hammond Pole branding
✅ **Lazy loading** for routes and modals
✅ **useDeferredValue** for search inputs
✅ **React Hook Form + Zod** for forms
✅ **parseApiError** for consistent error handling
✅ **Feature folders** with api/, components/, hooks/, types/
✅ **Theme support** (light/dark mode with Hammond Pole colors)
✅ **Tests** for components, API integration, and accessibility

---

**Generated for:** Hammond Pole Operations Platform
**Stack:** React 18, TypeScript, Vite, Redux Toolkit, RTK Query, TailwindCSS, shadcn/ui
**Purpose:** Guide for building consistent, high-quality frontend features
