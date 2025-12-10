---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Security Guidelines

> Based on OWASP Top 10:2025, security best practices, and industry standards.

## OWASP Top 10:2025 Quick Reference

| Rank | Category | Key Mitigation |
|------|----------|----------------|
| A01 | Broken Access Control | Deny by default, validate permissions server-side |
| A02 | Security Misconfiguration | Secure defaults, remove unused features |
| A03 | Software Supply Chain | Audit dependencies, verify integrity |
| A04 | Cryptographic Failures | Use strong algorithms, protect keys |
| A05 | Injection | Parameterized queries, input validation |
| A06 | Insecure Design | Threat modeling, secure design patterns |
| A07 | Authentication Failures | MFA, strong password policies |
| A08 | Integrity Failures | Verify signatures, secure CI/CD |
| A09 | Logging Failures | Log security events, alert on anomalies |
| A10 | Exception Handling | Fail securely, don't expose internals |

---

## Secrets Management

### NEVER Commit Secrets
```bash
# .gitignore - MANDATORY entries
.env
.env.*
!.env.example
*.pem
*.key
*.p12
*.pfx
secrets/
credentials.json
service-account*.json
```

### Environment Variables
```typescript
// GOOD: Validate required env vars at startup
function validateEnv(): void {
  const required = ['DATABASE_URL', 'JWT_SECRET', 'API_KEY'];
  const missing = required.filter((key) => !process.env[key]);

  if (missing.length > 0) {
    throw new Error(`Missing required environment variables: ${missing.join(', ')}`);
  }
}

// GOOD: Type-safe config
interface Config {
  databaseUrl: string;
  jwtSecret: string;
  apiKey: string;
}

function loadConfig(): Config {
  validateEnv();
  return {
    databaseUrl: process.env.DATABASE_URL!,
    jwtSecret: process.env.JWT_SECRET!,
    apiKey: process.env.API_KEY!,
  };
}

// BAD: Hardcoded secrets
const API_KEY = 'sk-1234567890abcdef'; // NEVER DO THIS
```

### Secret Rotation
- Use short-lived tokens when possible
- Implement graceful secret rotation
- Never log secrets (even partially)

---

## Input Validation (A05: Injection Prevention)

### Validate ALL External Input
```typescript
import { z } from 'zod';

// Define strict schemas
const UserInputSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100).regex(/^[a-zA-Z\s'-]+$/),
  age: z.number().int().min(0).max(150).optional(),
  role: z.enum(['user', 'admin', 'guest']),
});

type UserInput = z.infer<typeof UserInputSchema>;

// Validate at system boundaries
function createUser(input: unknown): User {
  const validated = UserInputSchema.parse(input); // Throws on invalid
  return saveUser(validated);
}

// Sanitize for display
function sanitizeForDisplay(text: string): string {
  return text
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#x27;');
}
```

### Validation Rules by Type
| Input Type | Validation |
|------------|------------|
| Email | Format + length + domain allowlist (optional) |
| Username | Alphanumeric + length limits |
| Numbers | Range + type (int/float) |
| URLs | Protocol whitelist (https) + domain validation |
| File uploads | Type + size + content validation |
| Free text | Length + encoding + sanitization |

---

## SQL Injection Prevention

### Always Use Parameterized Queries
```typescript
// GOOD: Parameterized query (Prisma)
const user = await prisma.user.findUnique({
  where: { id: userId },
});

// GOOD: Parameterized query (raw SQL)
const user = await db.query(
  'SELECT * FROM users WHERE id = $1 AND status = $2',
  [userId, 'active']
);

// GOOD: Query builder with parameters
const users = await knex('users')
  .where('email', email)
  .andWhere('deleted_at', null);

// BAD: String concatenation (VULNERABLE)
const user = await db.query(
  `SELECT * FROM users WHERE id = '${userId}'`  // SQL INJECTION!
);

// BAD: Template literal (VULNERABLE)
const user = await db.query(
  `SELECT * FROM users WHERE name LIKE '%${searchTerm}%'`  // SQL INJECTION!
);
```

### Safe Dynamic Queries
```typescript
// GOOD: Whitelist allowed columns
const ALLOWED_SORT_COLUMNS = ['name', 'email', 'created_at'] as const;
type SortColumn = typeof ALLOWED_SORT_COLUMNS[number];

function buildQuery(sortBy: string): Query {
  if (!ALLOWED_SORT_COLUMNS.includes(sortBy as SortColumn)) {
    throw new Error('Invalid sort column');
  }
  return knex('users').orderBy(sortBy);
}
```

---

## XSS Prevention (Cross-Site Scripting)

### React Auto-Escaping
```tsx
// SAFE: React auto-escapes by default
return <div>{userInput}</div>;

// DANGEROUS: Bypasses escaping
return <div dangerouslySetInnerHTML={{ __html: userInput }} />;  // AVOID!

// If HTML is needed, sanitize first
import DOMPurify from 'dompurify';

function SafeHTML({ html }: { html: string }) {
  const sanitized = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a', 'p', 'br'],
    ALLOWED_ATTR: ['href', 'target', 'rel'],
  });
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

### Content Security Policy
```typescript
// Express middleware
app.use((req, res, next) => {
  res.setHeader(
    'Content-Security-Policy',
    "default-src 'self'; " +
    "script-src 'self'; " +
    "style-src 'self' 'unsafe-inline'; " +
    "img-src 'self' data: https:; " +
    "connect-src 'self' https://api.example.com"
  );
  next();
});
```

---

## Authentication (A07)

### Password Handling
```typescript
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 12; // Minimum 10, recommended 12+

// Hash password
async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

// Verify password (constant-time comparison)
async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}

// Password requirements
const PASSWORD_REQUIREMENTS = {
  minLength: 12,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecial: true,
  maxLength: 128,
};
```

### Session Management
```typescript
// Secure session configuration
const sessionConfig = {
  secret: process.env.SESSION_SECRET!,
  name: 'sessionId', // Don't use default 'connect.sid'
  cookie: {
    httpOnly: true,      // Prevent XSS access
    secure: true,        // HTTPS only
    sameSite: 'strict',  // CSRF protection
    maxAge: 3600000,     // 1 hour
  },
  resave: false,
  saveUninitialized: false,
};
```

### JWT Best Practices
```typescript
import jwt from 'jsonwebtoken';

// GOOD: Secure JWT configuration
const JWT_CONFIG = {
  algorithm: 'RS256' as const,  // Use RSA, not HS256
  expiresIn: '15m',             // Short expiry
  issuer: 'your-app',
  audience: 'your-app-users',
};

function generateToken(userId: string): string {
  return jwt.sign(
    { sub: userId, type: 'access' },
    privateKey,
    JWT_CONFIG
  );
}

function verifyToken(token: string): JWTPayload {
  return jwt.verify(token, publicKey, {
    algorithms: ['RS256'],
    issuer: 'your-app',
    audience: 'your-app-users',
  }) as JWTPayload;
}
```

---

## Access Control (A01)

### Principle of Least Privilege
```typescript
// GOOD: Deny by default, explicit permissions
interface Permission {
  resource: string;
  action: 'read' | 'write' | 'delete' | 'admin';
}

function hasPermission(user: User, required: Permission): boolean {
  return user.permissions.some(
    (p) => p.resource === required.resource && p.action === required.action
  );
}

// Middleware
function requirePermission(permission: Permission) {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!hasPermission(req.user, permission)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}

// Usage
app.delete(
  '/api/users/:id',
  requirePermission({ resource: 'users', action: 'delete' }),
  deleteUser
);
```

### IDOR Prevention (Insecure Direct Object Reference)
```typescript
// BAD: Direct ID access without ownership check
app.get('/api/documents/:id', async (req, res) => {
  const doc = await Document.findById(req.params.id);
  res.json(doc); // Anyone can access any document!
});

// GOOD: Verify ownership
app.get('/api/documents/:id', async (req, res) => {
  const doc = await Document.findOne({
    _id: req.params.id,
    ownerId: req.user.id,  // Scoped to current user
  });

  if (!doc) {
    return res.status(404).json({ error: 'Not found' });
  }
  res.json(doc);
});
```

---

## CORS Configuration

```typescript
// GOOD: Specific origins
const corsOptions = {
  origin: (origin, callback) => {
    const allowedOrigins = [
      'https://myapp.com',
      'https://admin.myapp.com',
    ];

    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 86400, // 24 hours
};

// BAD: Too permissive
const corsOptions = {
  origin: '*',           // Allows any origin
  credentials: true,     // Dangerous with wildcard origin!
};
```

---

## Rate Limiting

```typescript
import rateLimit from 'express-rate-limit';

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,
  message: { error: 'Too many requests, please try again later' },
  standardHeaders: true,
  legacyHeaders: false,
});

// Stricter limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 5,
  message: { error: 'Too many login attempts' },
  skipSuccessfulRequests: true,
});

app.use('/api/', apiLimiter);
app.use('/api/auth/', authLimiter);
```

---

## Dependency Security (A03: Supply Chain)

### Regular Audits
```bash
# Check for vulnerabilities
npm audit

# Fix automatically (when safe)
npm audit fix

# Check for outdated packages
npm outdated

# Use lockfiles
# Always commit package-lock.json
```

### SBOM (Software Bill of Materials)
```bash
# Generate SBOM
npx @cyclonedx/cyclonedx-npm --output-file sbom.json
```

### Integrity Verification
```json
// package.json
{
  "scripts": {
    "preinstall": "npx npm-audit-resolver",
    "prepare": "husky install"
  }
}
```

---

## Error Handling (A10)

### Don't Expose Internal Details
```typescript
// GOOD: Generic error for clients, detailed logging
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  // Log full error internally
  logger.error('Request failed', {
    error: err.message,
    stack: err.stack,
    path: req.path,
    userId: req.user?.id,
  });

  // Return generic message to client
  if (err instanceof ValidationError) {
    return res.status(400).json({ error: 'Invalid input' });
  }

  if (err instanceof AuthenticationError) {
    return res.status(401).json({ error: 'Authentication required' });
  }

  // Never expose stack traces or internal errors
  res.status(500).json({ error: 'Internal server error' });
});

// BAD: Exposes internals
app.use((err, req, res, next) => {
  res.status(500).json({
    error: err.message,      // May contain sensitive info
    stack: err.stack,        // Exposes code structure
    query: req.query,        // Exposes user input
  });
});
```

---

## Security Headers

```typescript
import helmet from 'helmet';

app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https:"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true,
  },
  referrerPolicy: { policy: 'strict-origin-when-cross-origin' },
}));
```

---

## Security Checklist

### Before Every Commit
- [ ] No secrets in code
- [ ] No `console.log` with sensitive data
- [ ] Input validation on all endpoints
- [ ] Parameterized queries (no string concatenation)

### Before Every Release
- [ ] `npm audit` shows no high/critical vulnerabilities
- [ ] Dependencies are up to date
- [ ] Security headers configured
- [ ] Rate limiting enabled
- [ ] Error messages don't leak internals
- [ ] Access control tested
- [ ] CORS properly configured
- [ ] Logging captures security events

### Periodic Review
- [ ] Rotate secrets/keys
- [ ] Review access permissions
- [ ] Check for new OWASP guidelines
- [ ] Penetration testing
- [ ] Review third-party integrations
