# Testing Strategy Reference

## Test Pyramid

```
        /\
       /  \       E2E Tests (few, expensive, slow)
      /----\
     /      \     Integration Tests (moderate, realistic)
    /--------\
   /          \   Unit Tests (many, fast, isolated)
  /------------\
```

- **Unit Tests**: 70% of tests. Fast, isolated, test pure logic.
- **Integration Tests**: 20% of tests. Test database, HTTP, external services.
- **E2E Tests**: 10% of tests. Test critical user journeys end-to-end.

## Unit Testing

### What to Test
- Pure functions (calculations, transformations, validations)
- Business logic (rules, state machines, workflows)
- Utility functions (formatters, parsers, helpers)
- Error handling paths

### What NOT to Test
- Framework code (test your code, not the framework)
- Third-party libraries (they test their own code)
- Getters/setters without logic
- Code that only delegates to other functions

### Test Structure (Arrange-Act-Assert)
```typescript
describe('calculateOrderTotal', () => {
  it('applies 10% discount for orders over $100', () => {
    // Arrange
    const items = [
      { price: 6000, quantity: 1 },
      { price: 5000, quantity: 1 },
    ];

    // Act
    const total = calculateOrderTotal(items);

    // Assert
    expect(total).toBe(9900); // 11000 - 10% = 9900
  });

  it('returns zero for empty order', () => {
    expect(calculateOrderTotal([])).toBe(0);
  });

  it('throws for negative prices', () => {
    expect(() => calculateOrderTotal([{ price: -100, quantity: 1 }]))
      .toThrow('Price must be non-negative');
  });
});
```

### Mocking Rules
- Mock external services (APIs, databases, filesystem)
- Do NOT mock things you own (use real implementations)
- Mock at the boundary, not in the middle of business logic
- Verify mock calls only when the call itself is the behavior being tested

## Integration Testing

### Database Integration
```typescript
// Use testcontainers for real database in tests
import { PostgreSqlContainer } from '@testcontainers/postgresql';

describe('UserRepository', () => {
  let container: StartedPostgreSqlContainer;
  let db: Database;

  beforeAll(async () => {
    container = await new PostgreSqlContainer().start();
    db = new Database(container.getConnectionUri());
    await db.migrate();
  });

  afterAll(async () => {
    await container.stop();
  });

  beforeEach(async () => {
    await db.query('DELETE FROM users');
  });

  it('creates and retrieves a user', async () => {
    const user = await db.users.create({
      email: 'test@example.com',
      name: 'Test User',
    });

    const found = await db.users.findById(user.id);
    expect(found).toEqual(user);
  });
});
```

### HTTP Integration
```typescript
describe('POST /v1/users', () => {
  it('creates a user and returns 201', async () => {
    const response = await request(app)
      .post('/v1/users')
      .send({ email: 'test@example.com', name: 'Test' })
      .expect(201);

    expect(response.body.data).toHaveProperty('id');
    expect(response.body.data.email).toBe('test@example.com');
  });

  it('returns 422 for invalid email', async () => {
    const response = await request(app)
      .post('/v1/users')
      .send({ email: 'not-an-email', name: 'Test' })
      .expect(422);

    expect(response.body.errors).toContainEqual(
      expect.objectContaining({ field: 'email' })
    );
  });

  it('returns 409 for duplicate email', async () => {
    await request(app)
      .post('/v1/users')
      .send({ email: 'dup@example.com', name: 'First' });

    const response = await request(app)
      .post('/v1/users')
      .send({ email: 'dup@example.com', name: 'Second' })
      .expect(409);

    expect(response.body.errors[0].code).toBe('CONFLICT');
  });
});
```

### External Service Integration
```typescript
describe('PaymentService', () => {
  it('processes payment with Stripe', async () => {
    // Use Stripe test mode
    const result = await paymentService.charge({
      amount: 1000,
      currency: 'usd',
      source: 'tok_visa', // Stripe test token
    });

    expect(result.status).toBe('succeeded');
    expect(result.amount).toBe(1000);
  });

  it('handles payment failure gracefully', async () => {
    const result = await paymentService.charge({
      amount: 1000,
      currency: 'usd',
      source: 'tok_chargeDeclined',
    });

    expect(result.status).toBe('failed');
    expect(result.error).toBeDefined();
  });
});
```

## E2E Testing

### When to Use
- Critical user journeys (signup, login, checkout)
- Cross-service workflows
- Regression testing for high-impact bugs
- Smoke tests after deployment

### What NOT to E2E Test
- Every happy path (too expensive to maintain)
- UI styling (use visual regression instead)
- Edge cases (unit tests are better)
- Performance (use dedicated load testing)

### Example
```typescript
describe('User Registration Flow', () => {
  it('completes registration end-to-end', async () => {
    // 1. Register
    const registerRes = await request(app)
      .post('/v1/auth/register')
      .send({ email: 'new@example.com', password: 'securepassword' })
      .expect(201);

    const userId = registerRes.body.data.id;

    // 2. Verify email (simulate clicking link)
    const token = await getVerificationToken(userId);
    await request(app)
      .post('/v1/auth/verify-email')
      .send({ token })
      .expect(200);

    // 3. Login
    const loginRes = await request(app)
      .post('/v1/auth/login')
      .send({ email: 'new@example.com', password: 'securepassword' })
      .expect(200);

    expect(loginRes.body.data).toHaveProperty('accessToken');

    // 4. Access protected resource
    await request(app)
      .get('/v1/users/me')
      .set('Authorization', `Bearer ${loginRes.body.data.accessToken}`)
      .expect(200);
  });
});
```

## Contract Testing

### Consumer-Driven Contracts
```typescript
// Consumer test (defines expectations)
const pact = new Pact({
  consumer: 'WebApp',
  provider: 'UserService',
});

pact.addInteraction({
  state: 'user exists',
  uponReceiving: 'a request for user details',
  withRequest: {
    method: 'GET',
    path: '/v1/users/123',
  },
  willRespondWith: {
    status: 200,
    body: Matchers.like({
      id: Matchers.string('123'),
      email: Matchers.string('user@example.com'),
      name: Matchers.string('Test User'),
    }),
  },
});
```

### Provider Verification
```typescript
// Provider test (verifies contract)
const verifier = new Verifier({
  provider: 'UserService',
  providerBaseUrl: 'http://localhost:3000',
  pactUrls: ['http://pact-broker/pacts/WebApp/UserService/latest'],
});

await verifier.verifyProvider();
```

## Load Testing

### Tools
- k6 (JavaScript, developer-friendly)
- Artillery (YAML/JS, scenario-based)
- Locust (Python, distributed)
- Gatling (Scala, high performance)

### k6 Example
```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 50 },    // Ramp up to 50 users
    { duration: '1m', target: 50 },     // Stay at 50 users
    { duration: '30s', target: 200 },   // Spike to 200 users
    { duration: '1m', target: 200 },    // Stay at 200 users
    { duration: '30s', target: 0 },     // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],   // 95% of requests < 500ms
    http_req_failed: ['rate<0.01'],     // Error rate < 1%
  },
};

export default function () {
  const res = http.get('https://api.example.com/v1/users');
  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });
  sleep(1);
}
```

### Load Testing Checklist
- [ ] Test with production-like data volume
- [ ] Test with production-like traffic patterns
- [ ] Identify breaking point (when does it fail?)
- [ ] Monitor database connections, CPU, memory during test
- [ ] Test cache hit rates under load
- [ ] Test queue depth under load
- [ ] Verify graceful degradation (not catastrophic failure)

## Security Testing

### SAST (Static Application Security Testing)
- Analyze source code for vulnerabilities
- Run in CI, block on critical findings
- Tools: Semgrep, CodeQL, SonarQube

### DAST (Dynamic Application Security Testing)
- Test running application for vulnerabilities
- Automated scanning of all endpoints
- Tools: OWASP ZAP, Burp Suite

### Dependency Scanning
```yaml
- name: npm audit
  run: npm audit --audit-level=high

- name: Snyk test
  run: snyk test --severity-threshold=high
```

### Secret Scanning
```yaml
- name: gitleaks
  run: gitleaks detect --report-format json --report-path gitleaks-report.json
```

## Test Data Management

### Factories (Not Fixtures)
```typescript
function createUserFactory(overrides = {}) {
  return {
    email: `user-${Date.now()}@example.com`,
    password: 'securepassword123',
    name: 'Test User',
    role: 'user',
    ...overrides,
  };
}

// Each test creates its own data
it('updates user email', async () => {
  const user = await db.users.create(createUserFactory());
  // Test with this specific user
});
```

### Rules
- Each test creates its own data
- Tests run in isolation (no shared state)
- Clean up after each test (transactions that rollback, or truncate)
- Use unique values (timestamps, UUIDs) to avoid collisions
- Parallel test execution requires isolated databases or transactions

## CI/CD Integration

```yaml
name: Test Pipeline
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run typecheck

      - name: Unit tests
        run: npm run test:unit -- --coverage

      - name: Integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgres://postgres:test@localhost:5432/test

      - name: Security scan
        run: npm audit --audit-level=high

      - name: Upload coverage
        uses: codecov/codecov-action@v4
```
