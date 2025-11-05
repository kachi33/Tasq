# Project Architecture

## Overview
Tasq is a modern, offline-first task management application built with Next.js 16, TypeScript, and PostgreSQL. This document outlines the project's architecture, folder structure, and key technical decisions.

## Tech Stack Summary
- **Framework**: Next.js 16 (App Router)
- **Language**: TypeScript 5.9
- **Database**: PostgreSQL with Prisma ORM
- **Styling**: Tailwind CSS 4
- **State Management**: TanStack Query v5 + React Context
- **UI Components**: ShadCN UI (Radix UI primitives)

## Folder Structure

```
tasq/
├── public/                    # Static assets
│   ├── manifest.json         # PWA manifest
│   ├── service-worker.js     # Service worker for offline support
│   └── *.svg                 # SVG assets and images
├── prisma/                   # Database schema and migrations
│   └── schema.prisma        # Prisma schema definition
├── src/
│   ├── app/                 # Next.js App Router pages
│   │   ├── page.tsx        # Home dashboard
│   │   ├── tasks/          # Tasks page
│   │   │   └── page.tsx
│   │   ├── api/            # API routes
│   │   │   ├── todos/
│   │   │   ├── lists/
│   │   │   └── stats/
│   │   ├── layout.tsx      # Root layout
│   │   └── globals.css     # Global styles
│   ├── components/          # React components
│   │   ├── ui/             # ShadCN UI components
│   │   │   ├── button.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── HomeCard.tsx
│   │   │   └── ...
│   │   ├── ProgressChart.tsx      # Overall progress visualization
│   │   ├── StreakCard.tsx         # Streak tracking with calendar
│   │   ├── TodayActivityCard.tsx  # Today's task activity
│   │   ├── QuotesCard.tsx         # Motivational quotes
│   │   ├── WeeklyProductivityCard.tsx  # Weekly chart (future use)
│   │   ├── TaskItem.tsx           # Task creation/editing form
│   │   ├── Sidebar.tsx            # Task detail sidebar
│   │   ├── HomeTodoList.tsx       # Dashboard task preview
│   │   ├── FilterModal.tsx        # Advanced filtering
│   │   ├── CreateList.tsx         # List management
│   │   ├── WelcomeModal.tsx       # Onboarding modal
│   │   └── ...
│   ├── contexts/            # React Context providers
│   │   ├── SidebarContext.tsx  # Sidebar state management
│   │   └── ThemeContext.tsx    # Theme state management
│   ├── lib/                 # Utility functions & API
│   │   ├── api.ts          # API client functions
│   │   ├── offlineApi.ts   # Offline-first API with fallback
│   │   ├── session.ts      # Session management
│   │   └── dateUtils.ts    # Date formatting utilities
│   ├── providers/           # Provider wrappers
│   │   └── QueryProvider.tsx  # TanStack Query provider
│   └── types/               # TypeScript type definitions
│       └── index.ts
├── .env                     # Environment variables
├── package.json
└── README.md
```

## Component Organization

### Page Components
- **app/page.tsx**: Main dashboard with productivity cards carousel and upcoming tasks preview
- **app/tasks/page.tsx**: Full task list with filtering, pagination, and list management

### Dashboard Cards (Carousel)
- **ProgressChart**: Circular progress visualization showing overall completion (completed/total)
- **StreakCard**: Streak tracking with flame icon and weekly calendar visualization
- **TodayActivityCard**: Today's task activity with progress bar (created vs completed)
- **QuotesCard**: Motivational quotes with refresh functionality
- **WeeklyProductivityCard**: Weekly activity bar chart (reserved for future timestamp feature)

### Feature Components
- **TaskItem**: Comprehensive form for creating and editing tasks with:
  - Title and details
  - Due date and time selection
  - Priority levels (urgent, high, medium, low)
  - List assignment
  - Completion status
- **Sidebar**: Slide-out panel for task details and editing
- **HomeTodoList**: Dashboard preview showing next 3 upcoming tasks
- **FilterModal**: Advanced filtering interface (priority, status, list)
- **CreateList**: Modal for creating and editing custom lists
- **WelcomeModal**: Multi-step onboarding experience for new users

### Layout Components
- **SidebarWrapper**: Manages sidebar state and task selection
- **ThemeToggle**: Dark/light mode switcher with system preference
- **OfflineStatus**: Network status indicator
- **KeyboardShortcuts**: Global keyboard shortcut handler
- **PWAProvider**: Progressive Web App functionality
- **InstallPrompt**: PWA installation prompt

## Data Flow

### 1. Server State (TanStack Query)

**Query Keys:**
- `["todos"]` - All todos for current session
- `["todos", listId]` - Todos filtered by list
- `["todoLists"]` - All custom lists
- `["userStats"]` - Productivity statistics

**Features:**
- Automatic caching with stale-while-revalidate
- Cache invalidation on mutations
- Optimistic updates for instant UI feedback
- Automatic refetching on window focus
- Background updates

**Example Pattern:**
```typescript
const { data: todos, isLoading } = useQuery({
  queryKey: ["todos"],
  queryFn: () => fetchTodos(),
});
```

### 2. Client State (React Context)

**SidebarContext:**
- Sidebar open/close state
- Selected todo for editing
- Create mode vs edit mode
- Methods: `openSidebar`, `closeSidebar`, `openCreateMode`

**ThemeContext:**
- Current theme (light/dark)
- Theme toggle function
- Persisted in localStorage
- System preference detection

### 3. Session Management

**Flow:**
1. User visits application
2. Session ID generated (UUID) if none exists
3. Stored in localStorage (`session-id` key)
4. Sent as `X-Session-ID` header with all API requests
5. Server filters todos by session ID

**Benefits:**
- Data isolation without authentication
- Simple implementation for MVP
- Easy to migrate to proper auth later
- Suitable for demo/single-user scenarios

### 4. Offline Support

**Architecture:**
- **Service Worker**: Caches static assets and API responses
- **Offline API Layer** (`offlineApi.ts`): Intelligent routing
  - Online: Direct API calls through `api.ts`
  - Offline: Returns cached data or creates pending operations
  - Auto-sync when connection restored

**Offline Flow:**
1. User performs action (create/update/delete todo)
2. `offlineApi` detects online/offline state
3. If offline: Stores operation locally with negative ID
4. If online: Direct API call
5. Service worker syncs pending operations on reconnection

**Optimistic Updates:**
- UI updates immediately using `queryClient.setQueryData`
- Shows operation as "pending sync" if offline
- Automatic rollback on API errors
- Retry logic for failed operations

## Database Schema

### TodoList Table
```prisma
model TodoList {
  id         Int      @id @default(autoincrement())
  name       String
  color      String
  userId     String
  todos      Todo[]   @relation("TodoListToTodo")
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt
}
```

### Todo Table
```prisma
model Todo {
  id         Int      @id @default(autoincrement())
  title      String
  detail     String?
  priority   String   @default("low")
  due_date   String?
  due_time   String?
  end_time   String?
  end_date   String?
  completed  Boolean  @default(false)
  list_id    Int?
  list       TodoList? @relation("TodoListToTodo", fields: [list_id], references: [id])
  userId     String
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt
}
```

### Relationships
- **TodoList** has many **Todos** (one-to-many)
- **Todo** belongs to one **TodoList** (optional, nullable foreign key)
- Both models include **userId** for session-based isolation

## API Routes

All API routes are located in `src/app/api/` and follow RESTful conventions.

### Todos Endpoints
- **GET** `/api/todos` - Fetch all todos for current session (optional `?list_id` filter)
- **GET** `/api/todos/:id` - Fetch single todo by ID
- **POST** `/api/todos` - Create new todo
- **PUT** `/api/todos/:id` - Update existing todo
- **DELETE** `/api/todos/:id` - Delete todo (soft delete to trash)

### Todo Lists Endpoints
- **GET** `/api/lists` - Fetch all lists for current session
- **POST** `/api/lists` - Create new list
- **PUT** `/api/lists/:id` - Update existing list
- **DELETE** `/api/lists/:id` - Delete list

### Statistics Endpoint
- **GET** `/api/stats` - Fetch productivity statistics
  - Returns: total_todos, completed_todos, pending_todos, completion_rate, todos_created_today, todos_completed_today, active_streak, total_productivity_score

**Authentication:**
All routes use session-based isolation via `X-Session-ID` header. No password or JWT required.

## State Management Patterns

### Query Pattern (Data Fetching)
```typescript
const { data, isLoading, isError, refetch } = useQuery({
  queryKey: ["todos"],
  queryFn: () => fetchTodos(),
});
```

### Mutation Pattern (Data Modification)
```typescript
const mutation = useMutation({
  mutationFn: createTodo,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["todos"] });
    toast.success("Task created!");
  },
  onError: (error) => {
    toast.error("Failed to create task");
  },
});
```

### Optimistic Update Pattern
```typescript
// Update UI immediately
queryClient.setQueryData(["todos"], (old) => [...old, newTodo]);

// Then sync with server
await createTodo(newTodo);

// Auto-rollback on error
```

## Key Technical Decisions

### Why Next.js App Router?
- **Server Components**: Better performance, reduced client bundle
- **Built-in API Routes**: No separate backend needed
- **File-based Routing**: Intuitive and scalable
- **Excellent TypeScript Support**: First-class type safety
- **SEO-friendly**: Server-side rendering out of the box

### Why TanStack Query?
- **Automatic Caching**: Reduces unnecessary network requests
- **Background Updates**: Keep data fresh automatically
- **Optimistic Updates**: Instant UI feedback
- **Built-in States**: Loading, error, success states included
- **Devtools**: Excellent debugging experience
- **Request Deduplication**: Prevents duplicate API calls

### Why Prisma?
- **Type-safe Queries**: Auto-generated TypeScript types
- **Migration Management**: Easy database schema evolution
- **Excellent PostgreSQL Support**: Full feature support
- **Developer Experience**: Intuitive query API
- **Schema as Code**: Version-controlled database schema

### Why Session-based Auth?
- **Quick MVP**: Fast to implement without auth complexity
- **No Security Overhead**: No password hashing, JWT management
- **Easy Migration Path**: Can add proper auth layer later
- **Demo-friendly**: No signup required for trying the app
- **Data Isolation**: Session ID provides basic privacy

### Why Offline-First?
- **Better UX**: Works without internet
- **PWA Benefits**: Installable, fast, reliable
- **Resilience**: Handles poor network conditions
- **Modern Standard**: Expected feature for productivity apps

## Performance Optimizations

### Frontend
- **Server Components**: Static rendering where possible
- **Code Splitting**: Dynamic imports for heavy components
- **TanStack Query Caching**: Reduce API calls
- **Optimistic Updates**: Instant UI feedback
- **Image Optimization**: `next/image` for automatic optimization
- **Font Optimization**: `next/font` for font loading

### Backend
- **Database Indexing**: Indexed userId and list_id columns
- **Efficient Queries**: Prisma query optimization
- **Session-based Filtering**: Fast lookups without complex auth

### Network
- **Request Deduplication**: TanStack Query prevents duplicate calls
- **Background Sync**: Service Worker handles offline operations
- **Stale-While-Revalidate**: Show cached data while fetching updates

## Security Considerations

### Current Implementation
- **Environment Variables**: Sensitive data (DATABASE_URL) in `.env`
- **SQL Injection Prevention**: Prisma parameterized queries
- **Input Validation**: Client and server-side validation
- **Session Isolation**: Data filtered by session ID
- **HTTPS**: Required in production

### Future Enhancements
- **User Authentication**: Add proper login system
- **Rate Limiting**: Prevent API abuse
- **CSRF Protection**: Add CSRF tokens
- **XSS Prevention**: Already handled by React
- **Content Security Policy**: Add CSP headers

## Testing Strategy (Future)

### Planned Tests
- **Unit Tests**: Component and utility function tests (Vitest)
- **Integration Tests**: API route testing
- **E2E Tests**: Full user flow testing (Playwright)
- **Accessibility Tests**: WCAG compliance audits
- **Performance Tests**: Lighthouse CI

---

For contribution guidelines and development setup, see [CONTRIBUTING.md](./CONTRIBUTING.md).
For general information, see [README.md](./README.md).
