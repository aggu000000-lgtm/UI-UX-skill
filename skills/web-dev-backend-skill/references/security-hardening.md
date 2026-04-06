# Security Hardening Reference

## OWASP Top 10 (2021) Mitigations

### A01: Broken Access Control
- Enforce authorization on every endpoint
- Deny by default. Explicit allow per role/permission
- Never rely on obscurity (hidden URLs, unlinked resources)
- Test access control: user A cannot access user B's resources
- Log access control failures

### A02: Cryptographic Failures
- TLS 1.3 for all traffic in transit
- Encrypt sensitive data at rest (AES-256-GCM)
- Password hashing: argon2id (preferred) or bcrypt (cost >= 12)
- Never use MD5, SHA-1, DES, RC4, or ECB mode
- Rotate encryption keys annually or on compromise
- Use established crypto libraries (libsodium, OpenSSL). Never roll your own.

### A03: Injection
- Parameterized queries or ORM for all database access
- Input validation at the boundary (type, format, range, length)
- Output encoding for HTML, JavaScript, CSS, URL contexts
- Use allowlists, not blocklists, for input validation
- Sanitize file paths to prevent path traversal

### A04: Insecure Design
- Threat modeling during design phase
- Security requirements as part of user stories
- Abuse cases, not just use cases
- Rate limiting, account lockout, anomaly detection
- Principle of least privilege for all components

### A05: Security Misconfiguration
- Remove default accounts, passwords, and sample data
- Disable directory listing, verbose error messages, debug mode in production
- Security headers on all responses
- Automated configuration validation in CI/CD
- Minimal platform: remove unused services, ports, features

### A06: Vulnerable and Outdated Components
- Dependency scanning in CI (npm audit, Snyk, Dependabot)
- Pin dependency versions
- Block deployment on known critical/high vulnerabilities
- Maintain a Software Bill of Materials (SBOM)
- Subscribe to security advisories for all dependencies

### A07: Identification and Authentication Failures
- Enforce strong password policies (min 8 chars, check against breached password lists)
- MFA for all user accounts (WebAuthn/passkeys preferred)
- Secure session management (httpOnly, secure, sameSite cookies)
- Rate limit authentication attempts
- Secure password recovery (time-limited, single-use tokens)

### A08: Software and Data Integrity Failures
- Verify integrity of all downloads, updates, and plugins
- Use signed artifacts in CI/CD pipeline
- Validate deserialized data (never trust serialized input)
- Immutable infrastructure: replace, do not patch

### A09: Security Logging and Monitoring Failures
- Structured logging (JSON) with correlation IDs
- Log all authentication events (success, failure, lockout)
- Log access control failures
- Log input validation failures
- Never log secrets, tokens, passwords, PII
- Centralized log aggregation with alerting

### A10: Server-Side Request Forgery (SSRF)
- Validate and sanitize all user-supplied URLs
- Use allowlists for permitted domains/IPs
- Block requests to internal IP ranges (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16, 127.0.0.0/8, ::1)
- Disable HTTP redirects or limit redirect depth
- Use network-level controls (security groups, egress filtering)

## OWASP API Security Top 10 (2023) Mitigations

### API1: Broken Object Level Authorization (BOLA)
- Verify ownership on every request: `WHERE user_id = current_user.id`
- Use UUIDs instead of sequential IDs to prevent enumeration
- Implement resource-level authorization middleware

### API2: Broken Authentication
- Implement rate limiting on auth endpoints
- Use strong password hashing (argon2id, bcrypt)
- JWT: validate signature, expiration, issuer, audience
- Refresh token rotation and revocation

### API3: Broken Object Property Level Authorization
- Define allowed properties per role/operation in API schema
- Reject unexpected properties (strict schema validation)
- Mass assignment protection: whitelist allowed fields

### API4: Unrestricted Resource Consumption
- Rate limiting per user, per IP, per endpoint
- Request size limits
- Pagination on all list endpoints
- Query complexity limits (GraphQL)
- Timeout on all external calls

### API5: Broken Function Level Authorization
- Admin endpoints require admin role
- Default deny for all endpoints
- Separate routes for admin and user operations
- Log all admin actions

### API6: Unrestricted Access to Sensitive Business Flows
- Rate limit business-critical flows (purchases, registrations, password resets)
- CAPTCHA or bot detection for high-value operations
- Anomaly detection for unusual patterns
- Step-up authentication for sensitive operations

### API7: Server Side Request Forgery
- See A10 above
- Additional: validate URL schemes (http/https only)
- Use DNS rebinding protection

### API8: Security Misconfiguration
- See A05 above
- Additional: CORS properly configured
- API versioning with deprecation policy
- Consistent error responses (no stack traces)

### API9: Improper Inventory Management
- API documentation for all endpoints
- Decommission unused endpoints
- Monitor for shadow APIs
- API gateway as single entry point

### API10: Unsafe Consumption of APIs
- Validate all data received from external APIs
- Use allowlists for expected response schemas
- Timeout and circuit breaker for external API calls
- TLS verification for all outbound connections

## Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 0
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'
```

## Input Validation

### Rules
- Validate at the API boundary, before business logic
- Reject invalid input, do not sanitize
- Validate type, format, range, length, and allowed values
- Use a validation library (Zod, Joi, Yup, Pydantic)

### Example (Zod)
```typescript
const createUserSchema = z.object({
  email: z.string().email().max(255),
  password: z.string().min(8).max(128),
  name: z.string().min(1).max(100),
  role: z.enum(['user', 'admin']).default('user'),
});
```

### SQL Injection Prevention
```typescript
// BAD
const query = `SELECT * FROM users WHERE email = '${email}'`;

// GOOD
const query = 'SELECT * FROM users WHERE email = $1';
const result = await db.query(query, [email]);
```

## CORS Configuration

```typescript
// GOOD: Explicit allowlist
const corsOptions = {
  origin: ['https://app.example.com', 'https://admin.example.com'],
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Request-ID'],
  credentials: true,
  maxAge: 86400,
};

// BAD: Wildcard with credentials (will be rejected by browsers)
const corsOptions = {
  origin: '*',
  credentials: true,
};
```

## Secrets Management

### Never
- Commit secrets to version control
- Store secrets in environment files in the repository
- Log secrets or tokens
- Hardcode secrets in application code

### Always
- Use a secrets manager (AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager)
- Rotate secrets regularly (90 days for API keys, 365 days for certificates)
- Scope secrets to minimum permissions
- Audit secret access

## Dependency Security

### CI Pipeline
```yaml
- name: Dependency audit
  run: npm audit --audit-level=high

- name: Snyk security scan
  run: snyk test --severity-threshold=high

- name: Secret scanning
  run: gitleaks detect --report-format json
```

### Rules
- Pin all dependency versions
- Review dependency changes before updating
- Remove unused dependencies
- Monitor for new vulnerabilities (automated alerts)
- Prefer well-maintained dependencies with active communities
