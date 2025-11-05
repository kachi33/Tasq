# TASQ

A modern, full-featured Todo List application built with **Next.js 16**, **TypeScript**, **PostgreSQL**, and **Prisma**. Features include todo lists, task management, filtering, dark mode, offline support, and productivity tracking.



You can view tasq in action [here](https://kachi-todo.vercel.app//) 

![Tasq](./docs/screenshots/tasq-screenshot.png)

## Key Features Explained

### Session-Based Isolation
Each user gets a unique session ID stored in localStorage, ensuring their todos remain private without requiring authentication.

### Offline Support
The app includes a service worker and offline API fallback to ensure functionality even without an internet connection.

### Optimistic Updates
Using TanStack Query's optimistic update pattern, UI changes appear instantly before server confirmation, with automatic rollback on errors.

### Smart Filtering
Filter todos by:
- **Priority**: urgent, high, medium, low
- **Status**: all, completed, pending
- **List**: filter by specific todo list

### Dark Mode
Seamless dark mode toggle using next-themes with system preference detection and persistent user preference.

## Project Architecture

For detailed information about the project structure, component organization, and data flow, see [ARCHITECTURE.md](./ARCHITECTURE.md).

**Quick Overview:**
- **Frontend**: Next.js 16 with App Router, React Server Components
- **State Management**: TanStack Query for server state, React Context for UI state
- **Database**: PostgreSQL with Prisma ORM
- **Offline Support**: Service Worker with IndexedDB fallback
- **Styling**: Tailwind CSS 4 with ShadCN UI components


## Installation & Setup

### Prerequisites
- Node.js 18+ installed
- PostgreSQL database (local or cloud like Render)
- npm or yarn package manager

### 1. Clone the Repository

```bash
git clone https://github.com/kachi33/tasq.git
cd kachi-todo
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Environment Setup

Create a `.env` file in the root directory:

```env
# Database
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=public"
```

### 4. Database Setup

Run Prisma migrations to create the database tables:

```bash
# Generate Prisma Client
npx prisma generate

# Apply migrations to your database
npx prisma migrate deploy

# Or push schema directly (development)
npx prisma db push
```

### 5. Start Development Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

## Future Improvements

-  User registration and login
-  Multi-device sync
-  Task categories and tags
-  File attachments
-  Task sharing and collaboration
-  Calendar view
-  Kanban board view
-  Productivity trends and insights
-  Internationalization (i18n)
-  Email and push notifications


## Contributing

All contributions are Welcome! Whether it's bug fixes, new features, or documentation improvements, your help is appreciated.

**Quick Start:**
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes 
4. Test and build locally
4. Commit your changes (`git commit -m 'Add amazing feature'`)
5. Push to the branch (`git push origin feature/amazing-feature`)
6. Open a Pull Request

**For detailed contribution guidelines, development setup, and code standards, see [CONTRIBUTING.md](./CONTRIBUTING.md).**

### Development Guidelines
- Maintain full TypeScript compatibility
- Follow existing code patterns
- Ensure components are accessible (WCAG compliant)
- Test on multiple screen sizes
- Add proper type definitions

## License

This project is open source and available under the [MIT License](LICENSE).

## Author

**Kachi**
- GitHub: [@kachi33](https://github.com/kachi33)
---

Built by Kachi 
