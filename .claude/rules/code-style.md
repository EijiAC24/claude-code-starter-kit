---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Code Style Guidelines

## Formatting

- Use 2-space indentation (not tabs)
- Maximum line length: 100 characters
- Use single quotes for strings (except to avoid escaping)
- Always use semicolons
- No trailing whitespace
- End files with a single newline

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Variables | camelCase | `userName`, `isActive` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES`, `API_URL` |
| Functions | camelCase | `getUserData()`, `validateInput()` |
| Classes | PascalCase | `UserService`, `HttpClient` |
| Interfaces | PascalCase (no I prefix) | `User`, `ApiResponse` |
| Types | PascalCase | `ButtonProps`, `UserRole` |
| Files | kebab-case | `user-service.ts`, `api-client.ts` |
| React Components | PascalCase | `UserProfile.tsx`, `Button.tsx` |

## Import Organization

Order imports as follows:

```typescript
// 1. Built-in modules
import fs from 'fs';
import path from 'path';

// 2. External packages
import React from 'react';
import axios from 'axios';

// 3. Internal modules (absolute paths)
import { UserService } from '@/services/user';
import { config } from '@/config';

// 4. Relative imports
import { Button } from './Button';
import styles from './styles.module.css';

// 5. Type imports (separate with blank line)
import type { User, UserRole } from '@/types';
```

## TypeScript Specific

- Always annotate function parameters and return types
- Prefer `interface` for object shapes, `type` for unions/primitives
- Use `unknown` instead of `any` when type is uncertain
- Prefer `readonly` for immutable properties
- Use discriminated unions for type-safe error handling

```typescript
// Good
function processUser(id: string): Promise<User | null> {
  return fetchUser(id);
}

// Avoid
function processUser(id): any {
  return fetchUser(id);
}
```

## Functions

- Keep functions small (< 30 lines recommended)
- Single responsibility principle
- Use early returns to reduce nesting
- Prefer arrow functions for callbacks

```typescript
// Good - early return
function validateUser(user: User): boolean {
  if (!user.email) return false;
  if (!user.name) return false;
  return true;
}

// Avoid - deep nesting
function validateUser(user: User): boolean {
  if (user.email) {
    if (user.name) {
      return true;
    }
  }
  return false;
}
```

## Comments

- Explain WHY, not WHAT
- Use `//` for single-line comments
- Use `/** */` for documentation (JSDoc)
- Keep comments up-to-date with code
- Delete commented-out code (use version control)

## Error Handling

- Never silently ignore errors
- Use try-catch for async operations
- Provide meaningful error messages
- Log errors with context

```typescript
try {
  const data = await fetchData(id);
  return data;
} catch (error) {
  logger.error('Failed to fetch data', { id, error });
  throw new DataFetchError(`Could not fetch data for id: ${id}`);
}
```
