---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Security Guidelines

## CRITICAL: Secrets Management

**NEVER** commit secrets to version control:
- API keys and tokens
- Database credentials
- Private keys and certificates
- Passwords and passphrases
- OAuth client secrets

```bash
# Always add to .gitignore
.env
.env.*
*.pem
*.key
secrets/
credentials.json
```

## Environment Variables

```typescript
// Good - use environment variables
const apiKey = process.env.API_KEY;
if (!apiKey) {
  throw new Error('API_KEY environment variable is required');
}

// Bad - hardcoded secrets
const apiKey = 'sk-1234567890abcdef'; // NEVER DO THIS
```

## Input Validation

Validate ALL external input at system boundaries:

```typescript
// Use validation libraries (zod, joi, yup)
import { z } from 'zod';

const UserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150),
});

function createUser(input: unknown) {
  const validated = UserSchema.parse(input); // Throws on invalid
  return saveUser(validated);
}
```

## SQL Injection Prevention

```typescript
// Good - parameterized queries
const user = await db.query(
  'SELECT * FROM users WHERE id = $1',
  [userId]
);

// Good - ORM with parameterization
const user = await prisma.user.findUnique({
  where: { id: userId }
});

// BAD - string concatenation
const user = await db.query(
  `SELECT * FROM users WHERE id = '${userId}'` // VULNERABLE
);
```

## XSS Prevention

```typescript
// React auto-escapes by default - this is safe
return <div>{userInput}</div>;

// DANGEROUS - avoid dangerouslySetInnerHTML
return <div dangerouslySetInnerHTML={{ __html: userInput }} />;

// If HTML is needed, sanitize first
import DOMPurify from 'dompurify';
const sanitized = DOMPurify.sanitize(userInput);
```

## Authentication & Authorization

- Use established libraries (passport, next-auth, etc.)
- Implement proper session management
- Use HTTPS for all credential transmission
- Hash passwords with bcrypt/argon2 (never MD5/SHA1)
- Implement rate limiting on auth endpoints

```typescript
// Password hashing
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12;
const hashedPassword = await bcrypt.hash(password, SALT_ROUNDS);
const isValid = await bcrypt.compare(password, hashedPassword);
```

## CORS Configuration

```typescript
// Be specific about allowed origins
const corsOptions = {
  origin: ['https://myapp.com', 'https://api.myapp.com'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
};

// Avoid in production
origin: '*' // Too permissive
```

## Dependency Security

```bash
# Regular audits
npm audit
npm audit fix

# Check for updates
npm outdated

# Use lockfiles
package-lock.json  # Always commit this
```

## Security Checklist

Before deploying:
- [ ] No secrets in code or version control
- [ ] All user inputs validated
- [ ] SQL queries parameterized
- [ ] XSS protections in place
- [ ] HTTPS enabled
- [ ] Passwords properly hashed
- [ ] Rate limiting configured
- [ ] CORS properly configured
- [ ] Dependencies audited
- [ ] Error messages don't leak sensitive info

## Reporting Security Issues

If you find a security vulnerability:
1. Do NOT commit the fix publicly first
2. Report to security team/maintainers privately
3. Wait for coordinated disclosure
