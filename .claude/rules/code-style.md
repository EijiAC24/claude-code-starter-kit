---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Code Style Guidelines

> Based on Google TypeScript Style Guide, Airbnb JavaScript Style Guide, and industry best practices.

## Formatting

- Use 2-space indentation (not tabs)
- Maximum line length: 100 characters
- Use single quotes for strings (except to avoid escaping)
- Always use semicolons (prevents ASI issues)
- No trailing whitespace
- End files with a single newline
- One blank line between function definitions
- Two blank lines between class definitions

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Variables | camelCase | `userName`, `isActive` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES`, `API_URL` |
| Functions | camelCase | `getUserData()`, `validateInput()` |
| Classes | PascalCase | `UserService`, `HttpClient` |
| Interfaces | PascalCase (no I prefix) | `User`, `ApiResponse` |
| Types | PascalCase | `ButtonProps`, `UserRole` |
| Enums | PascalCase (members too) | `UserRole.Admin` |
| Files | kebab-case | `user-service.ts`, `api-client.ts` |
| React Components | PascalCase | `UserProfile.tsx`, `Button.tsx` |
| Private members | No underscore prefix | Use `private` modifier |

### Naming Tips
- Names should describe purpose, not implementation
- Avoid redundant type info in names (not `userString`, just `userName`)
- Boolean variables: use `is`, `has`, `can`, `should` prefix
- Functions: use verb phrases (`getUser`, `validateEmail`, `handleClick`)

## Import Organization

Order imports with blank lines between groups:

```typescript
// 1. Built-in modules
import fs from 'fs';
import path from 'path';

// 2. External packages (alphabetized)
import axios from 'axios';
import React from 'react';

// 3. Internal modules (absolute paths)
import { config } from '@/config';
import { UserService } from '@/services/user';

// 4. Relative imports (parent before siblings)
import { utils } from '../utils';
import { Button } from './Button';
import styles from './styles.module.css';

// 5. Type-only imports (always last)
import type { User, UserRole } from '@/types';
```

### Import Rules
- Use named exports (avoid `default` exports per Google style)
- Group and sort imports alphabetically within sections
- Use `type` keyword for type-only imports
- Prefer namespace imports for large APIs

## TypeScript Best Practices

### Type Annotations
```typescript
// Always annotate parameters and return types
function processUser(id: string): Promise<User | null> {
  return fetchUser(id);
}

// Annotate complex inferred types
const config: AppConfig = {
  apiUrl: process.env.API_URL,
  timeout: 5000,
};
```

### Type vs Interface
```typescript
// Use interface for object shapes (extendable)
interface User {
  id: string;
  name: string;
  email: string;
}

// Use type for unions, intersections, primitives
type UserRole = 'admin' | 'user' | 'guest';
type Nullable<T> = T | null;
type UserWithRole = User & { role: UserRole };
```

### Avoid `any`
```typescript
// BAD: any defeats type safety
function process(data: any): any { ... }

// GOOD: use unknown for truly unknown types
function process(data: unknown): Result {
  if (isValidData(data)) {
    return transform(data);
  }
  throw new Error('Invalid data');
}

// GOOD: use generics for flexible types
function process<T>(data: T): ProcessedData<T> { ... }
```

### Utility Types
```typescript
// Leverage built-in utility types
type PartialUser = Partial<User>;           // All optional
type RequiredUser = Required<User>;         // All required
type ReadonlyUser = Readonly<User>;         // Immutable
type UserPreview = Pick<User, 'id' | 'name'>;
type UserWithoutEmail = Omit<User, 'email'>;
type StringKeys = Record<string, string>;
```

### Discriminated Unions for Type Safety
```typescript
type Result<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function handleResult<T>(result: Result<T>) {
  if (result.success) {
    console.log(result.data);  // TypeScript knows data exists
  } else {
    console.error(result.error);  // TypeScript knows error exists
  }
}
```

## Functions

### Keep Functions Small & Focused
- Maximum ~30 lines (excluding comments/blank lines)
- Single responsibility principle
- One level of abstraction per function

### Early Returns
```typescript
// GOOD: early returns reduce nesting
function validateUser(user: User): ValidationResult {
  if (!user.email) {
    return { valid: false, error: 'Email required' };
  }
  if (!isValidEmail(user.email)) {
    return { valid: false, error: 'Invalid email format' };
  }
  if (!user.name || user.name.length < 2) {
    return { valid: false, error: 'Name must be at least 2 characters' };
  }
  return { valid: true };
}

// AVOID: deep nesting
function validateUser(user: User): ValidationResult {
  if (user.email) {
    if (isValidEmail(user.email)) {
      if (user.name && user.name.length >= 2) {
        return { valid: true };
      }
    }
  }
  return { valid: false, error: '...' };
}
```

### Function Declarations vs Arrows
```typescript
// Use function declarations for named functions
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Use arrow functions for callbacks and short expressions
const doubled = numbers.map((n) => n * 2);
const handler = (event: Event) => { ... };
```

### Default Parameters
```typescript
// Use default parameters instead of conditional defaults
function createUser(name: string, role: UserRole = 'user'): User {
  return { name, role };
}
```

## Classes

### SOLID Principles
```typescript
// Single Responsibility: one reason to change
class UserRepository {
  async findById(id: string): Promise<User | null> { ... }
  async save(user: User): Promise<void> { ... }
}

// Open/Closed: extend without modifying
interface PaymentProcessor {
  process(amount: number): Promise<void>;
}

class StripeProcessor implements PaymentProcessor { ... }
class PayPalProcessor implements PaymentProcessor { ... }

// Dependency Inversion: depend on abstractions
class OrderService {
  constructor(private payment: PaymentProcessor) {}
}
```

### Class Structure Order
1. Static properties
2. Instance properties
3. Constructor
4. Static methods
5. Public methods
6. Private methods

## Comments & Documentation

### When to Comment
```typescript
// GOOD: explain WHY, not WHAT
// Using exponential backoff to handle rate limiting from external API
const delay = Math.pow(2, retryCount) * 1000;

// BAD: restating the obvious
// Increment counter by 1
counter++;
```

### JSDoc for Public APIs
```typescript
/**
 * Fetches user data from the API.
 *
 * @param id - The unique user identifier
 * @returns The user object or null if not found
 * @throws {NetworkError} When the API is unreachable
 *
 * @example
 * const user = await getUser('123');
 */
async function getUser(id: string): Promise<User | null> { ... }
```

### TODO Comments
```typescript
// TODO(username): Refactor this when API v2 is released
// FIXME: Race condition when multiple requests fire
// HACK: Workaround for browser bug, remove after Chrome 120
```

## Error Handling

### Always Handle Errors Explicitly
```typescript
// GOOD: proper error handling with context
async function fetchUser(id: string): Promise<User> {
  try {
    const response = await api.get(`/users/${id}`);
    return response.data;
  } catch (error) {
    logger.error('Failed to fetch user', { id, error });
    throw new UserFetchError(`Could not fetch user ${id}`, { cause: error });
  }
}

// BAD: swallowing errors
try {
  await riskyOperation();
} catch {
  // Silent failure - NEVER do this
}
```

### Custom Error Classes
```typescript
class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly statusCode: number = 500,
    options?: ErrorOptions
  ) {
    super(message, options);
    this.name = 'AppError';
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(`${resource} with id ${id} not found`, 'NOT_FOUND', 404);
    this.name = 'NotFoundError';
  }
}
```

### Only Throw Error Objects
```typescript
// GOOD: throw Error subclasses
throw new ValidationError('Invalid email format');

// BAD: throwing primitives (no stack trace)
throw 'Something went wrong';
throw 404;
```

## Async/Await

### Prefer async/await over .then()
```typescript
// GOOD: async/await
async function getData(): Promise<Data> {
  const user = await fetchUser();
  const posts = await fetchPosts(user.id);
  return { user, posts };
}

// AVOID: promise chains (harder to read)
function getData(): Promise<Data> {
  return fetchUser()
    .then((user) => fetchPosts(user.id)
      .then((posts) => ({ user, posts })));
}
```

### Parallel Execution
```typescript
// GOOD: parallel when independent
const [user, config] = await Promise.all([
  fetchUser(id),
  fetchConfig(),
]);

// Use Promise.allSettled for partial failure tolerance
const results = await Promise.allSettled([
  fetchUser(id),
  fetchPosts(id),
]);
```

## Immutability

### Prefer Immutable Patterns
```typescript
// GOOD: spread for immutable updates
const updatedUser = { ...user, name: 'New Name' };
const newArray = [...items, newItem];

// GOOD: use readonly for immutable properties
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}

// GOOD: as const for literal types
const ROLES = ['admin', 'user', 'guest'] as const;
type Role = typeof ROLES[number]; // 'admin' | 'user' | 'guest'
```

## Null & Undefined

### Prefer undefined over null
```typescript
// GOOD: use undefined for missing values
function findUser(id: string): User | undefined { ... }

// Use null only when explicitly representing "no value"
// (e.g., JSON parsing, database null values)
```

### Nullish Coalescing & Optional Chaining
```typescript
// Use ?? for default values (only null/undefined)
const name = user.name ?? 'Anonymous';

// Use || only when falsy values should trigger default
const count = input || 0;  // 0, '', false also trigger default

// Optional chaining for safe property access
const city = user?.address?.city;
const result = callback?.();
```
