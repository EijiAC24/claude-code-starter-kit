---
paths: tests/**/*.{ts,tsx,js,jsx}, **/*.test.{ts,tsx,js,jsx}, **/*.spec.{ts,tsx,js,jsx}
---

# Testing Guidelines

## Test File Naming

- Unit tests: `*.test.ts` or `*.spec.ts`
- Integration tests: `*.integration.test.ts`
- E2E tests: `*.e2e.test.ts`
- Place test files next to source or in `/tests` directory

## Test Naming Convention

Format: `should_[expected behavior]_when_[condition]`

```typescript
// Good
it('should_return_user_when_valid_id_provided', () => {});
it('should_throw_error_when_user_not_found', () => {});
it('should_send_email_when_registration_complete', () => {});

// Also acceptable: descriptive sentences
it('returns user data for valid IDs', () => {});
it('throws NotFoundError when user does not exist', () => {});
```

## Test Structure (AAA Pattern)

Always organize tests with Arrange-Act-Assert:

```typescript
describe('UserService', () => {
  describe('getUserById', () => {
    it('should_return_user_when_valid_id_provided', async () => {
      // Arrange
      const userId = 'user-123';
      const expectedUser = { id: userId, name: 'John' };
      mockUserRepository.findById.mockResolvedValue(expectedUser);

      // Act
      const result = await userService.getUserById(userId);

      // Assert
      expect(result).toEqual(expectedUser);
      expect(mockUserRepository.findById).toHaveBeenCalledWith(userId);
    });
  });
});
```

## Coverage Requirements

| Type | Minimum Coverage |
|------|-----------------|
| Overall | 80% |
| Critical paths (auth, payments) | 100% |
| Utility functions | 90% |
| UI components | 70% |

## What to Test

### DO Test
- Business logic and calculations
- Error handling and edge cases
- State changes and side effects
- API request/response handling
- User interactions (UI)
- Integration between modules

### DON'T Test
- External libraries (they have their own tests)
- Framework internals
- Simple getters/setters
- Private implementation details

## Mocking Best Practices

```typescript
// Mock only what you need
const mockUserRepo = {
  findById: jest.fn(),
  save: jest.fn(),
};

// Reset mocks between tests
beforeEach(() => {
  jest.clearAllMocks();
});

// Verify mock calls
expect(mockUserRepo.save).toHaveBeenCalledTimes(1);
expect(mockUserRepo.save).toHaveBeenCalledWith(
  expect.objectContaining({ name: 'John' })
);
```

## Async Testing

```typescript
// Use async/await
it('should_fetch_user_data', async () => {
  const result = await fetchUser('123');
  expect(result.name).toBe('John');
});

// Test errors
it('should_throw_when_api_fails', async () => {
  await expect(fetchUser('invalid')).rejects.toThrow('Not found');
});

// Test promises with .resolves/.rejects
await expect(promise).resolves.toBe(expected);
await expect(promise).rejects.toThrow(ErrorClass);
```

## Test Data

- Use factories or builders for test data
- Avoid hardcoded magic values
- Keep test data close to tests

```typescript
// Good - clear test data
const testUser = createTestUser({ name: 'Test User' });

// Avoid - magic values
const result = service.process({ x: 42, y: 'abc' });
```

## Running Tests

```bash
npm test                    # Run all tests
npm test -- --watch        # Watch mode
npm test -- --coverage     # With coverage report
npm test -- path/to/file   # Single file
npm test -- -t "pattern"   # Tests matching pattern
```
