---
paths: tests/**/*.{ts,tsx,js,jsx}, **/*.test.{ts,tsx,js,jsx}, **/*.spec.{ts,tsx,js,jsx}
---

# Testing Guidelines

> Based on Node.js Testing Best Practices, Jest/Vitest documentation, and industry standards.

## Testing Philosophy

- **Test behavior, not implementation** - Focus on public interfaces
- **Tests are documentation** - They should explain what the code does
- **Fast feedback** - Tests should run quickly
- **Reliable** - No flaky tests; if it fails, there's a real problem

## Test File Organization

### Naming Conventions
```
src/
├── services/
│   └── user-service.ts
tests/
├── unit/
│   └── services/
│       └── user-service.test.ts
├── integration/
│   └── api/
│       └── users.integration.test.ts
└── e2e/
    └── user-flow.e2e.test.ts
```

### File Naming
- Unit tests: `*.test.ts` or `*.spec.ts`
- Integration tests: `*.integration.test.ts`
- E2E tests: `*.e2e.test.ts`

## Test Naming Convention

### Descriptive Names
```typescript
// Pattern: should_[expected behavior]_when_[condition]
describe('UserService', () => {
  describe('createUser', () => {
    it('should_create_user_when_valid_data_provided', async () => {});
    it('should_throw_validation_error_when_email_invalid', async () => {});
    it('should_hash_password_when_user_created', async () => {});
  });
});

// Alternative: plain English (equally valid)
describe('UserService', () => {
  describe('createUser', () => {
    it('creates a user with valid data', async () => {});
    it('throws ValidationError for invalid email', async () => {});
    it('hashes the password before saving', async () => {});
  });
});
```

### Describe Block Organization
```typescript
describe('ComponentName or ModuleName', () => {
  // Group by method/function
  describe('methodName', () => {
    // Group by scenario
    describe('when condition is met', () => {
      it('does expected thing', () => {});
    });
  });
});
```

## AAA Pattern (Arrange-Act-Assert)

### Structure Every Test Consistently
```typescript
it('should_calculate_total_with_discount_when_coupon_applied', () => {
  // Arrange - Set up test data and dependencies
  const items = [
    { id: '1', price: 100, quantity: 2 },
    { id: '2', price: 50, quantity: 1 },
  ];
  const coupon = { code: 'SAVE10', discountPercent: 10 };
  const cart = new ShoppingCart(items);

  // Act - Execute the code under test
  const total = cart.calculateTotal(coupon);

  // Assert - Verify the results
  expect(total).toBe(225); // (200 + 50) * 0.9
});
```

### Keep Each Section Focused
```typescript
// GOOD: Clear separation
it('should_send_welcome_email_when_user_registers', async () => {
  // Arrange
  const emailService = createMockEmailService();
  const userService = new UserService(emailService);
  const userData = { email: 'test@example.com', name: 'Test' };

  // Act
  await userService.register(userData);

  // Assert
  expect(emailService.send).toHaveBeenCalledWith(
    expect.objectContaining({
      to: 'test@example.com',
      template: 'welcome',
    })
  );
});
```

## Coverage Guidelines

### Target Coverage Levels
| Type | Minimum | Target |
|------|---------|--------|
| Overall | 80% | 85%+ |
| Critical paths (auth, payments) | 95% | 100% |
| Business logic | 90% | 95%+ |
| Utility functions | 85% | 90%+ |
| UI components | 70% | 80%+ |

### Coverage Is Not Quality
```typescript
// High coverage, low quality - tests implementation
it('calls the database', () => {
  service.getUser('123');
  expect(db.query).toHaveBeenCalled(); // Just checks it was called
});

// Lower coverage, high quality - tests behavior
it('returns user data for valid ID', async () => {
  const user = await service.getUser('123');
  expect(user).toEqual({
    id: '123',
    name: 'John',
    email: 'john@example.com',
  });
});
```

## What to Test

### DO Test
- ✅ Public API behavior
- ✅ Business logic and calculations
- ✅ Error handling and edge cases
- ✅ State transitions
- ✅ User interactions (UI)
- ✅ Integration between modules
- ✅ Data transformations

### DON'T Test
- ❌ Private implementation details
- ❌ External libraries (they have their own tests)
- ❌ Framework internals
- ❌ Simple getters/setters with no logic
- ❌ Constants and configuration values

## Mocking Best Practices

### Mock Only What You Must
```typescript
// GOOD: Mock external dependencies
const mockApi = {
  getUser: vi.fn().mockResolvedValue({ id: '123', name: 'John' }),
};
const service = new UserService(mockApi);

// AVOID: Mocking internal implementation
// Don't mock private methods or internal state
```

### Mock Placement Rules
```typescript
// If mock directly affects test outcome → define in test (Arrange phase)
it('handles API error gracefully', async () => {
  mockApi.getUser.mockRejectedValue(new Error('Network error'));

  await expect(service.getUser('123')).rejects.toThrow('Failed to fetch user');
});

// If mock is setup for all tests → use beforeEach
beforeEach(() => {
  vi.clearAllMocks();
  mockApi.getUser.mockResolvedValue(defaultUser);
});
```

### Verify Mock Interactions
```typescript
it('calls API with correct parameters', async () => {
  await service.updateUser('123', { name: 'Jane' });

  expect(mockApi.updateUser).toHaveBeenCalledTimes(1);
  expect(mockApi.updateUser).toHaveBeenCalledWith('123', { name: 'Jane' });
});
```

### Partial Matching
```typescript
expect(mockFn).toHaveBeenCalledWith(
  expect.objectContaining({ email: 'test@example.com' })
);

expect(mockFn).toHaveBeenCalledWith(
  expect.stringContaining('error'),
  expect.any(Number)
);
```

## Async Testing

### Use async/await
```typescript
// GOOD: async/await
it('fetches user data', async () => {
  const user = await userService.getUser('123');
  expect(user.name).toBe('John');
});

// GOOD: Testing rejections
it('throws on invalid ID', async () => {
  await expect(userService.getUser('')).rejects.toThrow('Invalid ID');
});

// Using resolves/rejects matchers
await expect(promise).resolves.toBe(expected);
await expect(promise).rejects.toThrow(ErrorClass);
```

### Testing Timers
```typescript
beforeEach(() => {
  vi.useFakeTimers();
});

afterEach(() => {
  vi.useRealTimers();
});

it('retries after delay', async () => {
  const promise = service.fetchWithRetry();

  await vi.advanceTimersByTimeAsync(1000);

  await expect(promise).resolves.toBeDefined();
});
```

## Test Data Management

### Use Factories
```typescript
// test/factories/user.ts
export function createTestUser(overrides: Partial<User> = {}): User {
  return {
    id: 'test-id',
    name: 'Test User',
    email: 'test@example.com',
    createdAt: new Date('2024-01-01'),
    ...overrides,
  };
}

// Usage in tests
const user = createTestUser({ name: 'Custom Name' });
const adminUser = createTestUser({ role: 'admin' });
```

### Avoid Magic Values
```typescript
// BAD: Magic values with no context
expect(calculate(42, 'abc')).toBe(123);

// GOOD: Meaningful test data
const quantity = 5;
const pricePerUnit = 10.00;
const expectedTotal = 50.00;
expect(calculateTotal(quantity, pricePerUnit)).toBe(expectedTotal);
```

## Testing React Components

### Render and Query
```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

it('displays user name after loading', async () => {
  render(<UserProfile userId="123" />);

  // Wait for async content
  expect(await screen.findByText('John Doe')).toBeInTheDocument();
});

it('shows error message on failed load', async () => {
  mockApi.getUser.mockRejectedValue(new Error('Not found'));

  render(<UserProfile userId="invalid" />);

  expect(await screen.findByRole('alert')).toHaveTextContent('Failed to load');
});
```

### User Interactions
```typescript
it('submits form with user data', async () => {
  const user = userEvent.setup();
  const onSubmit = vi.fn();

  render(<LoginForm onSubmit={onSubmit} />);

  await user.type(screen.getByLabelText('Email'), 'test@example.com');
  await user.type(screen.getByLabelText('Password'), 'password123');
  await user.click(screen.getByRole('button', { name: 'Sign In' }));

  expect(onSubmit).toHaveBeenCalledWith({
    email: 'test@example.com',
    password: 'password123',
  });
});
```

### Query Priority (Testing Library)
1. `getByRole` - Accessible queries (best)
2. `getByLabelText` - Form fields
3. `getByPlaceholderText` - If no label
4. `getByText` - Non-interactive elements
5. `getByTestId` - Last resort

## Integration Testing

### Test Real Interactions
```typescript
describe('User API Integration', () => {
  let app: Express;
  let db: Database;

  beforeAll(async () => {
    db = await createTestDatabase();
    app = createApp(db);
  });

  afterAll(async () => {
    await db.close();
  });

  beforeEach(async () => {
    await db.clear();
  });

  it('creates and retrieves user', async () => {
    // Create
    const createRes = await request(app)
      .post('/api/users')
      .send({ name: 'John', email: 'john@example.com' });

    expect(createRes.status).toBe(201);
    const { id } = createRes.body;

    // Retrieve
    const getRes = await request(app).get(`/api/users/${id}`);

    expect(getRes.status).toBe(200);
    expect(getRes.body).toMatchObject({
      name: 'John',
      email: 'john@example.com',
    });
  });
});
```

## Test Commands

```bash
# Run all tests
npm test

# Watch mode
npm test -- --watch

# Coverage report
npm test -- --coverage

# Run specific file
npm test -- path/to/file.test.ts

# Run tests matching pattern
npm test -- -t "user service"

# Run only changed files
npm test -- --changed

# Update snapshots
npm test -- -u
```

## Common Anti-Patterns to Avoid

### 1. Testing Implementation Details
```typescript
// BAD: Testing private state
expect(service._cache.size).toBe(1);

// GOOD: Testing behavior
const result1 = await service.getData('key');
const result2 = await service.getData('key');
expect(mockApi.fetch).toHaveBeenCalledTimes(1); // Cached
```

### 2. Excessive Mocking
```typescript
// BAD: Mocking everything
vi.mock('./utils');
vi.mock('./helpers');
vi.mock('./validators');

// GOOD: Use real implementations when possible
// Only mock external boundaries (APIs, databases, etc.)
```

### 3. Flaky Tests
```typescript
// BAD: Time-dependent
expect(result.timestamp).toBe(Date.now());

// GOOD: Freeze time or use ranges
vi.setSystemTime(new Date('2024-01-01'));
expect(result.timestamp).toBe(new Date('2024-01-01').getTime());
```

### 4. Test Interdependence
```typescript
// BAD: Tests depend on order
let sharedUser;
it('creates user', () => { sharedUser = createUser(); });
it('updates user', () => { updateUser(sharedUser.id); }); // Fails if run alone

// GOOD: Each test is independent
it('updates user', () => {
  const user = createUser(); // Creates its own user
  updateUser(user.id);
});
```
