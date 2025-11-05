# Contributing to Tasq

Thank you for your interest in contributing! This document provides guidelines and instructions for contributing to the project.

## Table of Contents
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Code Standards](#code-standards)
- [Pull Request Process](#pull-request-process)
- [Commit Message Guidelines](#commit-message-guidelines)
- [Testing](#testing)
- [Need Help?](#need-help)

## Getting Started

### Prerequisites
- Node.js 18 or higher
- PostgreSQL database (local or cloud)
- npm or yarn
- Git
- Basic knowledge of TypeScript, React, and Next.js

### First Time Setup

1. **Fork the repository** on GitHub

2. **Clone your fork**
   ```bash
   git clone https://github.com/YOUR_USERNAME/tasq.git
   cd kachi-todo
   ```

3. **Add upstream remote**
   ```bash
   git remote add upstream https://github.com/kachi33/tasq.git
   ```

4. **Install dependencies**
   ```bash
   npm install
   ```

5. **Setup environment**
   ```bash
   cp .env.example .env
   # Edit .env with your database credentials
   ```

6. **Setup database**
   ```bash
   npx prisma generate
   npx prisma db push
   ```

7. **Start development server**
   ```bash
   npm run dev
   ```

## Development Setup

### Environment Variables
Required environment variables in `.env`:
```env
DATABASE_URL="postgresql://user:password@localhost:5432/tasq"
```

### Database Changes
When modifying the database schema:
```bash
# After editing prisma/schema.prisma
npx prisma generate

# For development
npx prisma db push

# For production (creates migration)
npx prisma migrate dev --name your_migration_name
```

### Running Checks
Before submitting a PR, run these commands:
```bash
# Type checking
npm run type-check

# Linting
npm run lint

# Build verification
npm run build
```

## Code Standards

### TypeScript
- All new code must be TypeScript
- Avoid `any` types - use proper types or `unknown`
- Define interfaces for all component props
- Export types that might be reused
- Use type inference where appropriate

**Example:**
```typescript
interface TaskItemProps {
  todo: Todo | null;
  onSave?: (todo: Todo) => void;
  onClose?: () => void;
}

export const TaskItem = ({ todo, onSave, onClose }: TaskItemProps) => {
  // Component code
};
```

### React Components
- Use functional components with hooks
- Use 'use client' directive only when necessary (client-side state, browser APIs)
- Extract reusable logic into custom hooks
- Keep components focused and single-purpose
- Prefer composition over prop drilling

**Example:**
```typescript
'use client';  // Only if needed

import { useState } from 'react';

export const MyComponent = () => {
  const [state, setState] = useState('');

  return (
    <div>{state}</div>
  );
};
```

### File Naming
- **Components**: PascalCase (e.g., `TaskItem.tsx`)
- **Utilities**: camelCase (e.g., `dateUtils.ts`)
- **Types**: camelCase (e.g., `index.ts` in types folder)
- **Page routes**: lowercase (e.g., `tasks/page.tsx`)
- **API routes**: lowercase (e.g., `api/todos/route.ts`)

### Code Style
- Use 2 spaces for indentation
- Use double quotes for strings
- Use semicolons
- Use trailing commas in multi-line objects/arrays
- Follow existing code patterns
- Maximum line length: 100 characters (soft limit)

### Accessibility
- All interactive elements must be keyboard accessible
- Use semantic HTML elements (`button`, `nav`, `main`, etc.)
- Include proper ARIA labels where needed
- Ensure color contrast meets WCAG AA standards
- Test with screen readers when possible
- Use ShadCN UI components (already accessible)

**Example:**
```typescript
// Good - accessible button
<button onClick={handleClick} aria-label="Delete task">
  <Trash2 />
</button>

// Bad - div button without accessibility
<div onClick={handleClick}>
  <Trash2 />
</div>
```

### Comments
- Add comments for complex logic
- Document why, not what
- Use JSDoc for exported functions/components
- Keep comments up-to-date with code changes

**Example:**
```typescript
/**
 * Calculates the completion percentage for the current week
 * @param todos - Array of todos to analyze
 * @returns Percentage (0-100) of completed tasks this week
 */
export const calculateWeeklyCompletion = (todos: Todo[]): number => {
  // Filter todos completed this week
  const thisWeekTodos = todos.filter(isCompletedThisWeek);

  // Calculate percentage
  return (thisWeekTodos.length / todos.length) * 100;
};
```

## Pull Request Process

### 1. Create a Feature Branch
```bash
git checkout -b feature/your-feature-name
# OR
git checkout -b fix/bug-description
# OR
git checkout -b docs/documentation-update
```

**Branch naming conventions:**
- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation changes
- `refactor/` - Code refactoring
- `style/` - UI/styling changes
- `test/` - Adding tests
- `chore/` - Maintenance tasks

### 2. Make Your Changes
- Keep changes focused and atomic
- Update relevant documentation
- Add/update TypeScript types
- Test on multiple screen sizes (375px, 768px, 1024px)
- Test in both light and dark modes
- Ensure no console errors or warnings

### 3. Commit Your Changes
Follow commit message guidelines (see below).

```bash
git add .
git commit -m "feat(tasks): add task duplication feature"
```

### 4. Sync with Upstream
```bash
git fetch upstream
git rebase upstream/main
```

If there are conflicts:
```bash
# Resolve conflicts manually
git add .
git rebase --continue
```

### 5. Push to Your Fork
```bash
git push origin feature/your-feature-name
```

### 6. Create Pull Request
- Go to the original repository on GitHub
- Click "New Pull Request"
- Select your branch
- Fill out the PR template (see below)
- Request review

### PR Template
```markdown
## Description
Brief description of changes and why they were made.

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Testing
- [ ] Tested locally
- [ ] Tested on mobile devices
- [ ] Tested in dark mode
- [ ] Type checks pass (`npm run type-check`)
- [ ] Linting passes (`npm run lint`)
- [ ] Build succeeds (`npm run build`)

## Screenshots (if applicable)
Add screenshots or GIFs demonstrating the changes.

## Related Issues
Closes #issue_number
Fixes #issue_number
```

## Commit Message Guidelines

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.

### Format
```
type(scope): subject

body (optional)

footer (optional)
```

### Types
- **feat**: New feature for the user
- **fix**: Bug fix for the user
- **docs**: Documentation changes
- **style**: Formatting, missing semicolons, etc. (no code change)
- **refactor**: Code refactoring (no functional changes)
- **test**: Adding or updating tests
- **chore**: Updating build tasks, package manager configs, etc.
- **perf**: Performance improvements
- **ci**: CI/CD changes

### Scopes
- **tasks**: Task management features
- **lists**: Todo lists features
- **ui**: UI components
- **api**: API routes
- **db**: Database changes
- **auth**: Authentication/session
- **pwa**: PWA/offline features

### Examples
```bash
# Good commit messages
feat(tasks): add task duplication feature
fix(sidebar): prevent sidebar from closing on outside click when editing
docs(readme): update installation instructions
refactor(api): simplify todo creation logic
style(ui): improve dark mode contrast for buttons

# Bad commit messages
update stuff
fix bug
WIP
changes
```

### Commit Message Rules
- Use imperative mood ("add" not "added" or "adds")
- Don't capitalize first letter
- No period at the end
- Keep subject line under 72 characters
- Separate subject from body with a blank line
- Use body to explain what and why, not how

## Testing

### Manual Testing Checklist
Before submitting a PR, test:

**Functionality:**
- [ ] Feature works as expected
- [ ] Edge cases handled
- [ ] Error states work correctly
- [ ] Loading states display properly

**Cross-browser:**
- [ ] Chrome/Edge (Chromium)
- [ ] Firefox
- [ ] Safari

**Responsive:**
- [ ] Mobile (375px width)
- [ ] Tablet (768px width)
- [ ] Desktop (1024px+ width)

**Themes:**
- [ ] Light mode works
- [ ] Dark mode works
- [ ] Theme switcher works

**Accessibility:**
- [ ] Keyboard navigation works
- [ ] Screen reader compatible
- [ ] ARIA labels present
- [ ] Color contrast sufficient

**Performance:**
- [ ] No console errors or warnings
- [ ] Network requests succeed/fail gracefully
- [ ] No memory leaks
- [ ] Offline functionality works (if applicable)

### Future: Automated Testing
We plan to add:
- **Unit Tests** with Vitest
- **Integration Tests** for API routes
- **E2E Tests** with Playwright
- **Visual Regression Tests**

## Common Issues

### Port Already in Use
```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Or use a different port
PORT=3001 npm run dev
```

### Database Connection Issues
1. Verify `DATABASE_URL` in `.env` file
2. Ensure PostgreSQL is running
3. Check database exists
4. Verify user permissions

```bash
# Test connection
psql $DATABASE_URL
```

### TypeScript Errors
```bash
# Run type checker
npm run type-check

# Clear and regenerate types
rm -rf node_modules/.cache
npx prisma generate
```

### Build Errors
```bash
# Clear all caches
rm -rf .next node_modules/.cache

# Reinstall dependencies
npm install

# Try building again
npm run build
```

### HMR/Turbopack Issues
```bash
# Clear Next.js cache
rm -rf .next

# Restart dev server
npm run dev
```

## Project Structure

For detailed information about the project architecture, see [ARCHITECTURE.md](./ARCHITECTURE.md).

**Quick reference:**
- **src/app/**: Next.js pages and API routes
- **src/components/**: React components
- **src/lib/**: Utility functions and API clients
- **src/contexts/**: React Context providers
- **src/types/**: TypeScript type definitions
- **prisma/**: Database schema

## Code Review Process

### What Reviewers Look For
- Code follows style guide
- Tests pass
- No breaking changes (unless intentional)
- Documentation updated
- Accessibility considered
- Performance not degraded
- Security best practices followed

### Responding to Feedback
- Be open to feedback
- Ask questions if unclear
- Make requested changes
- Update PR description if scope changes
- Thank reviewers for their time

## Need Help?

### Resources
- Check [ARCHITECTURE.md](./ARCHITECTURE.md) for project structure
- Review [README.md](./README.md) for setup instructions
- Read [Next.js documentation](https://nextjs.org/docs)
- Review [Prisma documentation](https://www.prisma.io/docs)
- Check [TanStack Query docs](https://tanstack.com/query/latest)

### Getting Support
- **Bugs**: Open a GitHub issue with reproduction steps
- **Questions**: Start a GitHub discussion
- **Features**: Open an issue with detailed proposal
- **Security**: Email maintainer directly (see README)

## Recognition

Contributors will be recognized in:
- README.md acknowledgments section
- Release notes
- GitHub contributors page
- Project documentation

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to tasq! Your efforts help make this project better for everyone.
