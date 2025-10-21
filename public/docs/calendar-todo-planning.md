# Perencanaan Fitur: Google Calendar API & To-Do List

## 📋 Overview

Dokumen ini merencanakan implementasi dua fitur utama untuk aplikasi personal web:
1. **Google Calendar Integration** - Sinkronisasi dengan Google Calendar
2. **To-Do List System** - Manajemen tugas personal
3. **Dashboard Widgets** - Widget eksport untuk dashboard utama

---

## 🗓️ Google Calendar Integration

### API Setup & Authentication

#### Required Google Cloud Setup
- **Google Cloud Project**: Create new project di Google Cloud Console
- **OAuth 2.0 Credentials**: 
  - Client ID (Web Application)
  - Client Secret
  - Authorized JavaScript origins
  - Authorized redirect URIs

#### Environment Variables
```env
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_API_KEY=your_google_api_key
GOOGLE_CLIENT_SECRET=your_google_client_secret
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_nextauth_secret
```

#### OAuth 2.0 Scopes
- **Read Only**: `https://www.googleapis.com/auth/calendar.readonly`
- **Full Access**: `https://www.googleapis.com/auth.calendar.events`

### Core Features

#### 1. Event Management
- **List Events**: Fetch upcoming events dengan pagination
- **Create Event**: Create new events dengan attendees & reminders
- **Update Event**: Edit existing events
- **Delete Event**: Remove events
- **Event Details**: View full event information

#### 2. Calendar Views
- **Month View**: Traditional monthly calendar
- **Week View**: Weekly timeline
- **Day View**: Daily schedule
- **Agenda View**: List of upcoming events

#### 3. Advanced Features
- **Recurring Events**: Support untuk recurring patterns
- **Multiple Calendars**: Sync multiple Google Calendars
- **Event Search**: Search events by title/description
- **Offline Support**: Cache events untuk offline access

### Technical Implementation

#### Client-side Integration
- Menggunakan Google API Client Library (gapi)
- Setup discovery document untuk Calendar API v3
- Implementasi OAuth 2.0 token management
- Event fetching dengan parameter filtering (timeMin, maxResults, orderBy)
- Error handling dan token refresh

#### Server-side API Routes
- API route untuk GET events (verify auth, fetch from Google, format response)
- API route untuk POST events (validate input, create via Google Calendar API)
- API route untuk PUT/PATCH events (update existing events)
- API route untuk DELETE events (remove events)
- Rate limiting dan caching middleware

---

## ✅ To-Do List System

### Database Schema (Prisma)

```prisma
model Task {
  id          String   @id @default(cuid())
  title       String
  description String?
  priority    Priority @default(MEDIUM)
  status      Status   @default(PENDING)
  dueDate     DateTime?
  categoryId  String?
  category    Category? @relation(fields: [categoryId], references: [id])
  tags        Tag[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Category {
  id          String   @id @default(cuid())
  name        String   @unique
  color       String   @default("#3B82F6")
  description String?
  tasks       Task[]
}

model Tag {
  id    String   @id @default(cuid())
  name  String   @unique()
  color String   @default("#6B7280")
  tasks Task[]
}

enum Priority {
  LOW
  MEDIUM
  HIGH
  URGENT
}

enum Status {
  PENDING
  IN_PROGRESS
  COMPLETED
  CANCELLED
}
```

### Core Features

#### 1. Task Management
- **CRUD Operations**: Create, Read, Update, Delete tasks
- **Task Details**: Rich descriptions, attachments, subtasks
- **Task Templates**: Reusable task templates
- **Bulk Actions**: Multiple task operations

#### 2. Organization
- **Categories**: Hierarchical categorization
- **Tags**: Flexible tagging system
- **Priority Levels**: 4-level priority system
- **Status Tracking**: Custom workflow statuses

#### 3. Time Management
- **Due Dates**: Date & time with timezone support
- **Reminders**: Multiple reminder types
- **Time Tracking**: Task duration tracking
- **Recurring Tasks**: Automated task creation

#### 4. Search & Filter
- **Advanced Search**: Full-text search across tasks
- **Smart Filters**: Saved filter combinations
- **Sorting**: Multiple sorting options
- **Quick Filters**: One-click filter presets

### Technical Implementation

#### Frontend Components
- **Task Form Component**: Interface untuk create/edit task dengan props untuk task data, submit handler, dan cancel handler
- **Task List Component**: Component untuk menampilkan daftar tasks dengan props untuk task array, update handler, dan delete handler
- **Task Card Component**: Single task display dengan status indicators dan action buttons
- **Filter Panel Component**: UI untuk search dan filter tasks
- **Category Manager Component**: Interface untuk manage categories dan tags

#### API Routes
- **GET /api/tasks**: Endpoint untuk fetch tasks dengan query parameters untuk filter dan sort
- **POST /api/tasks**: Endpoint untuk create new task dengan validation menggunakan Zod schema
- **PUT /api/tasks/[id]**: Endpoint untuk update existing task
- **DELETE /api/tasks/[id]**: Endpoint untuk delete task
- **GET /api/categories**: Endpoint untuk fetch categories
- **POST /api/categories**: Endpoint untuk create new category
- **GET /api/tags**: Endpoint untuk fetch tags

---

## 📊 Dashboard Widget Exports

### Google Calendar Widgets

#### 1. Upcoming Events Widget
- **Props**: maxEvents (number), showLocation (boolean), showAttendees (boolean)
- **Features**: 
  - Menampilkan 5 events mendatang
  - Time remaining indicators
  - Quick actions (view, join, edit)
  - Color-coded by calendar

#### 2. Today's Schedule Widget
- **Props**: view (timeline/list), showCompleted (boolean)
- **Features**: 
  - Timeline view untuk hari ini
  - Progress indicators untuk ongoing events
  - Quick event creation
  - Meeting join buttons

#### 3. Calendar Mini View Widget
- **Props**: height (number), showWeekNumbers (boolean)
- **Features**: 
  - Compact month calendar
  - Event dots pada dates
  - Navigation controls
  - Today highlight

#### 4. Quick Add Event Widget
- **Props**: defaultDuration (number), showReminders (boolean)
- **Features**: 
  - Simple event creation
  - Auto-suggest locations
  - Recurring options
  - Save templates

### To-Do List Widgets

#### 1. Tasks Overview Widget
- **Props**: timeRange (today/week/month), groupBy (status/priority/category)
- **Features**: 
  - Task statistics
  - Completion charts
  - Priority breakdown
  - Category distribution

#### 2. Today's Tasks Widget
- **Props**: showCompleted (boolean), sortBy (priority/dueDate/createdAt)
- **Features**: 
  - Checkbox interactions
  - Quick edit capability
  - Inline task creation
  - Drag & drop reordering

#### 3. High Priority Tasks Widget
- **Props**: includeOverdue (boolean), maxTasks (number)
- **Features**: 
  - Filtered high-priority items
  - Due date warnings
  - Escalation alerts
  - Quick action buttons

#### 4. Recent Completed Widget
- **Props**: limit (number), showUndo (boolean)
- **Features**: 
  - Recently finished tasks
  - Completion timestamps
  - Undo functionality
  - Celebration animations

---

## 🏗️ File Structure

```
src/
├── app/
│   ├── (dashboard)/
│   │   ├── calendar/
│   │   │   ├── page.tsx
│   │   │   ├── components/
│   │   │   │   ├── calendar-view.tsx
│   │   │   │   ├── event-form.tsx
│   │   │   │   ├── event-details.tsx
│   │   │   │   └── calendar-settings.tsx
│   │   │   └── api/
│   │   │       ├── events/route.ts
│   │   │       └── sync/route.ts
│   │   ├── tasks/
│   │   │   ├── page.tsx
│   │   │   ├── components/
│   │   │   │   ├── task-list.tsx
│   │   │   │   ├── task-form.tsx
│   │   │   │   ├── task-details.tsx
│   │   │   │   ├── category-manager.tsx
│   │   │   │   └── filter-panel.tsx
│   │   │   └── api/
│   │   │       ├── tasks/route.ts
│   │   │       ├── categories/route.ts
│   │   │       └── tags/route.ts
│   │   └── dashboard/
│   │       └── page.tsx
│   └── api/
│       ├── google/
│       │   ├── auth/
│       │   │   └── route.ts
│       │   └── calendar/
│       │       ├── events/route.ts
│       │       └── sync/route.ts
│       └── webhooks/
│           └── google/route.ts
├── components/
│   ├── widgets/
│   │   ├── calendar/
│   │   │   ├── upcoming-events.tsx
│   │   │   ├── today-schedule.tsx
│   │   │   ├── calendar-mini.tsx
│   │   │   └── quick-add-event.tsx
│   │   └── tasks/
│   │       ├── tasks-overview.tsx
│   │       ├── todays-tasks.tsx
│   │       ├── high-priority.tsx
│   │       └── recent-completed.tsx
│   ├── features/
│   │   ├── calendar/
│   │   │   ├── calendar-grid.tsx
│   │   │   ├── event-card.tsx
│   │   │   └── time-slot.tsx
│   │   └── tasks/
│   │       ├── task-card.tsx
│   │       ├── task-filters.tsx
│   │       └── task-stats.tsx
│   └── ui/
├── lib/
│   ├── google/
│   │   ├── calendar.ts
│   │   ├── auth.ts
│   │   └── types.ts
│   ├── db/
│   │   ├── schema.ts
│   │   ├── migrations/
│   │   └── seed.ts
│   ├── stores/
│   │   ├── calendar.ts
│   │   ├── tasks.ts
│   │   └── ui.ts
│   └── utils/
│       ├── date.ts
│       ├── validation.ts
│       └── formatting.ts
└── types/
    ├── calendar.ts
    ├── tasks.ts
    └── widgets.ts
```

---

## 📦 Required Dependencies

### Core Dependencies
```json
{
  "dependencies": {
    "@prisma/client": "^5.0.0",
    "prisma": "^5.0.0",
    "@tanstack/react-query": "^5.0.0",
    "zustand": "^4.4.0",
    "react-hook-form": "^7.45.0",
    "@hookform/resolvers": "^3.3.0",
    "zod": "^3.22.0",
    "date-fns": "^2.30.0",
    "recharts": "^2.8.0",
    "lucide-react": "^0.263.0",
    "next-auth": "^4.21.0",
    "@next-auth/prisma-adapter": "^1.0.6"
  }
}
```

### Development Dependencies
```json
{
  "devDependencies": {
    "@types/gapi": "^0.0.44",
    "@types/gapi.calendar": "^3.0.0",
    "prisma-client-lib": "^2.15.0"
  }
}
```

---

## 🔧 Implementation Steps

### Phase 1: Setup & Foundation
1. **Google Cloud Project Setup**
   - Create project & enable Calendar API
   - Configure OAuth 2.0 credentials
   - Set up environment variables

2. **Database Setup**
   - Initialize Prisma
   - Create database schema
   - Run migrations

3. **Authentication System**
   - Implement NextAuth.js
   - Configure Google Provider
   - Set up session management

### Phase 2: Google Calendar Integration
1. **API Client Setup**
   - Google API Client Library integration
   - Token management
   - Error handling

2. **Core Features**
   - Event listing & display
   - Event creation & editing
   - Calendar views implementation

3. **Advanced Features**
   - Recurring events
   - Multiple calendars
   - Offline support

### Phase 3: To-Do List System
1. **Backend Implementation**
   - API routes for tasks
   - Database operations
   - Validation & error handling

2. **Frontend Implementation**
   - Task management UI
   - Forms & validation
   - Search & filtering

3. **Advanced Features**
   - Categories & tags
   - Time tracking
   - Recurring tasks

### Phase 4: Dashboard Widgets
1. **Widget Development**
   - Calendar widgets
   - Task widgets
   - Widget configuration

2. **Dashboard Integration**
   - Widget grid layout
   - Drag & drop arrangement
   - Widget persistence

### Phase 5: Polish & Optimization
1. **UI/UX Enhancements**
   - Animations & transitions
   - Responsive design
   - Accessibility improvements

2. **Performance Optimization**
   - Caching strategies
   - Code splitting
   - Bundle optimization

3. **Testing & Deployment**
   - Unit tests
   - Integration tests
   - Production deployment

---

## 🎯 Success Metrics

### User Experience
- [ ] Fast page loads (< 2 seconds)
- [ ] Intuitive interface
- [ ] Mobile-responsive design
- [ ] Offline functionality

### Feature Completeness
- [ ] Full Google Calendar sync
- [ ] Complete task management
- [ ] Functional dashboard widgets
- [ ] Real-time updates

### Technical Quality
- [ ] 95%+ test coverage
- [ ] Zero security vulnerabilities
- [ ] Performance budget compliance
- [ ] Accessibility standards compliance

---

## 📝 Notes & Considerations

### Security
- Store Google credentials securely
- Implement proper token refresh
- Rate limiting for API calls
- Input validation & sanitization

### Performance
- Implement intelligent caching
- Optimize API calls
- Lazy loading for large datasets
- Background sync for offline support

### Scalability
- Design for multiple users
- Consider data storage limits
- Plan for API quota management
- Implement proper error recovery

---

*Dokumen ini akan diperbarui seiring perkembangan implementasi fitur.*
