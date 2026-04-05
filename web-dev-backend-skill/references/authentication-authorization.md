# Authentication & Authorization Reference

## Authentication vs Authorization

- **Authentication**: Verifying identity. "Who are you?"
- **Authorization**: Verifying permissions. "What are you allowed to do?"
- These are separate concerns. Implement separately. Compose together.

## Password Authentication

### Password Requirements
- Minimum 8 characters
- Check against breached password databases (Have I Been Pwned API)
- No maximum length restriction (or generous limit like 128 chars)
- No arbitrary composition rules (special chars, numbers, etc.) — length is the primary strength factor
- Display password strength meter (zxcvbn algorithm)

### Password Hashing

#### Argon2id (Preferred)
```typescript
import { hash, verify } from 'argon2';

const hash = await argon2.hash(password, {
  type: argon2.argon2id,
  memoryCost: 19456,    // 19 MB
  timeCost: 2,
  parallelism: 1,
});

const isValid = await argon2.verify(hash, password);
```

#### bcrypt (Alternative)
```typescript
import bcrypt from 'bcrypt';

const saltRounds = 12;
const hash = await bcrypt.hash(password, saltRounds);
const isValid = await bcrypt.compare(password, hash);
```

### Password Reset Flow
1. User requests reset with email
2. Generate cryptographically random token (32+ bytes)
3. Store token hash + expiry (1 hour) in database
4. Send reset link with plaintext token to email
5. User submits token + new password
6. Verify token, hash new password, invalidate token
7. Token is single-use. Expire immediately after use.

## Session Management

### Cookie-Based Sessions (Web Applications)
```typescript
res.cookie('session', sessionToken, {
  httpOnly: true,       // Not accessible via JavaScript
  secure: true,         // HTTPS only
  sameSite: 'lax',      // CSRF protection (strict for sensitive apps)
  maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
  path: '/',
});
```

### Session Storage
- Server-side session store (Redis, database)
- Session ID is random, unguessable (256-bit)
- Session data never stored in cookie
- Rotate session ID after authentication
- Invalidate sessions on password change

## JWT (JSON Web Tokens)

### When to Use
- Stateless authentication (microservices, mobile apps)
- Third-party token exchange (OAuth)
- Short-lived API tokens

### When NOT to Use
- Session management for web apps (use cookies instead)
- Storing sensitive data (JWT is encoded, not encrypted)
- Long-lived tokens (use refresh tokens instead)

### Token Structure
```typescript
// Access Token (short-lived)
{
  "sub": "user-uuid",
  "iat": 1704067200,
  "exp": 1704068100,    // 15 minutes
  "iss": "auth.example.com",
  "aud": "api.example.com",
  "scope": "read:users write:users"
}

// Refresh Token (long-lived, stored server-side)
{
  "sub": "user-uuid",
  "jti": "token-uuid",   // Unique identifier for revocation
  "iat": 1704067200,
  "exp": 1706659200,    // 30 days
  "iss": "auth.example.com"
}
```

### JWT Security
- Always use RS256 or ES256 (asymmetric). Never HS256 in production.
- Validate: signature, expiration (`exp`), issuer (`iss`), audience (`aud`)
- Refresh token rotation: issue new refresh token on each use
- Maintain revocation list for compromised tokens
- Store refresh tokens server-side (database or Redis)

## OAuth 2.1 / OpenID Connect

### Authorization Code Flow with PKCE (Mandatory for All Clients)
```
1. Client generates code_verifier (random string, 43-128 chars)
2. Client computes code_challenge = BASE64URL(SHA256(code_verifier))
3. Client redirects to authorization endpoint with code_challenge
4. User authenticates and authorizes
5. Authorization server returns authorization code
6. Client exchanges code + code_verifier for tokens
7. Authorization server validates code_challenge matches code_verifier
```

### Scopes
- Granular permissions: `read:users`, `write:orders`, `admin:settings`
- Principle of least privilege
- User-consented scopes displayed during authorization
- Refresh tokens inherit original scopes

### OIDC Claims
```json
{
  "sub": "user-uuid",
  "name": "John Doe",
  "email": "john@example.com",
  "email_verified": true,
  "picture": "https://example.com/avatar.jpg",
  "updated_at": 1704067200
}
```

## Multi-Factor Authentication

### WebAuthn / Passkeys (Preferred)
- Phishing-resistant
- No shared secrets
- Biometric or hardware key verification
- Supported by all major browsers and platforms

### TOTP (RFC 6238)
- Time-based one-time passwords
- 6-digit codes, 30-second intervals
- Backup codes generated at setup (store hashed)
- Clock skew tolerance: ±1 interval

### SMS-Based 2FA (Deprecated)
- Vulnerable to SIM swapping, SS7 attacks, interception
- Only as fallback when no other option available
- Warn users about insecurity

## Authorization Models

### RBAC (Role-Based Access Control)
```typescript
const roles = {
  admin: ['users:read', 'users:write', 'users:delete', 'settings:read', 'settings:write'],
  manager: ['users:read', 'users:write', 'orders:read', 'orders:write'],
  user: ['users:read:self', 'orders:read:self', 'orders:write:self'],
};

function hasPermission(role: string, permission: string): boolean {
  return roles[role]?.includes(permission) ?? false;
}
```

- Simple, well-understood
- Suitable for most applications
- Role explosion problem at scale

### ABAC (Attribute-Based Access Control)
```typescript
function canAccess(user: User, resource: Resource, action: string): boolean {
  return (
    user.department === resource.department &&
    user.clearanceLevel >= resource.requiredClearance &&
    action === 'read' &&
    resource.status !== 'archived'
  );
}
```

- Fine-grained, dynamic
- Policies based on attributes (user, resource, environment)
- More complex to implement and audit

### ReBAC (Relationship-Based Access Control)
```typescript
// Can user access this document?
// User -> member of -> Workspace -> owner of -> Document
function canAccessDocument(userId: string, documentId: string): boolean {
  const relationships = getRelationships(userId, documentId);
  return relationships.some(r => r.type === 'workspace_member' && r.workspace.owns(documentId));
}
```

- Google Docs-style permissions
- Permissions derived from relationships
- Used by Google Zanzibar, Spotify, Discord

## API Key Authentication

### For Machine-to-Machine Communication
- Prefix keys for identification: `sk_live_abc123...`
- Store hashed keys in database (like passwords)
- Scope keys to minimum permissions
- Rotate regularly
- Never use for user authentication

### Key Format
```
SECRET_KEY_REDACTED
│  │    │
│  │    └── Random bytes (hex encoded)
│  └─────── Environment (live, test, dev)
└────────── Key type (secret, public)
```

## Implementation Checklist

- [ ] Password hashing uses argon2id or bcrypt (cost >= 12)
- [ ] Session cookies are httpOnly, secure, sameSite
- [ ] JWT uses RS256 or ES256, validates all claims
- [ ] PKCE enforced for all OAuth flows
- [ ] MFA available (WebAuthn preferred, TOTP fallback)
- [ ] Rate limiting on authentication endpoints
- [ ] Account lockout after failed attempts (with exponential backoff)
- [ ] Session rotation after authentication
- [ ] Refresh token rotation and revocation
- [ ] Authorization checked on every endpoint
- [ ] Principle of least privilege enforced
- [ ] API keys scoped and rotated
- [ ] Password reset tokens are single-use, time-limited, hashed
