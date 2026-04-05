# Performance & Scaling Reference

## Response Time Targets

| Percentile | Target | Meaning |
|-----------|--------|---------|
| p50 | < 100ms | Median response time |
| p95 | < 500ms | 95% of requests faster than this |
| p99 | < 1s | 99% of requests faster than this |
| p99.9 | < 3s | Tail latency, indicates systemic issues |

## Caching Strategy

### Cache Layers

#### L1: In-Process Cache
- Fastest (sub-millisecond)
- Per-instance, not shared
- Use for: configuration, feature flags, static reference data
- Libraries: node-cache (Node.js), lru-cache, caffeine (Java)

#### L2: Distributed Cache (Redis/Memcached)
- Shared across instances
- Use for: session data, query results, rate limit counters, frequently accessed records
- TTL strategy: short TTL + background refresh (stale-while-revalidate)

#### L3: CDN Cache
- Edge-level caching
- Use for: static assets, API responses with Cache-Control, media
- Cache-Control headers: `public, max-age=3600, stale-while-revalidate=86400`

### Cache Invalidation Patterns

#### Time-Based (TTL)
```typescript
await redis.setex('user:123', 300, JSON.stringify(user)); // 5 minutes
```

#### Write-Through
```typescript
async function updateUser(id: string, data: UserUpdate) {
  const updated = await db.users.update(id, data);
  await redis.set(`user:${id}`, JSON.stringify(updated), 'EX', 300);
  return updated;
}
```

#### Cache-Aside (Lazy Loading)
```typescript
async function getUser(id: string): Promise<User> {
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  const user = await db.users.findById(id);
  if (user) {
    await redis.setex(`user:${id}`, 300, JSON.stringify(user));
  }
  return user;
}
```

#### Cache Stampede Prevention
```typescript
// Use distributed lock to prevent thundering herd
const lock = await redis.set('lock:user:123', '1', 'NX', 'EX', 10);
if (!lock) {
  // Another instance is refreshing, return stale or wait
  return getStaleData('user:123');
}
```

### Cache Keys
- Structured: `entity:id:field` or `query:hash`
- Include version for schema changes: `v2:user:123`
- Invalidate by prefix when bulk invalidation needed

## Database Query Optimization

### N+1 Query Problem
```typescript
// BAD: N+1 queries
const users = await db.users.findMany();
for (const user of users) {
  user.orders = await db.orders.findMany({ where: { userId: user.id } });
}

// GOOD: Single query with JOIN or IN clause
const users = await db.users.findMany({
  include: { orders: true },
});

// GOOD: DataLoader batching
const userLoader = new DataLoader(async (ids) => {
  const orders = await db.orders.findMany({
    where: { userId: { in: ids } },
  });
  return ids.map(id => orders.filter(o => o.userId === id));
});
```

### Query Planning
```sql
-- Check query execution plan
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT u.*, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.status = 'active'
GROUP BY u.id;
```

- Look for sequential scans on large tables
- Check index usage
- Monitor buffer hits vs reads
- Verify join order and method

### Connection Management
- Pool size: `(CPU cores * 2) + disk spindles`
- Monitor: active connections, idle connections, wait time
- Alert when pool utilization > 80%
- Use connection proxy (PgBouncer, ProxySQL) for high-concurrency apps

## Async Processing

### When to Use
- Email sending
- Image/video processing
- Report generation
- Webhook delivery
- Data exports
- Any operation taking > 500ms

### Message Queue Selection

| Queue | Best For | Characteristics |
|-------|----------|----------------|
| RabbitMQ | Complex routing, reliable delivery | Exchanges, queues, bindings, DLQ |
| Kafka | High throughput, event streaming | Partitions, consumer groups, retention |
| AWS SQS | Managed, simple | FIFO or standard, visibility timeout |
| Redis (Streams/Bull) | Lightweight, simple jobs | In-memory, persistence optional |
| Celery (Python) | Python ecosystems | Multiple brokers, result backends |

### Job Processing Patterns
```typescript
// Retry with exponential backoff
const job = await queue.add('send-email', { to, subject, body }, {
  attempts: 5,
  backoff: { type: 'exponential', delay: 1000 },
  removeOnComplete: true,
  removeOnFail: false, // Keep failed jobs for inspection
});

// Dead letter queue for failed jobs
// After max retries, move to DLQ for manual inspection
```

### Idempotent Job Processing
```typescript
async function processPayment(job: Job) {
  const { paymentId, amount, currency } = job.data;

  // Check if already processed (idempotency)
  const existing = await db.payments.findByExternalId(paymentId);
  if (existing) return existing;

  // Process payment
  const result = await paymentGateway.charge({ amount, currency });

  // Store result (with idempotency key)
  return db.payments.create({
    externalId: paymentId,
    status: result.status,
    gatewayResponse: result,
  });
}
```

## Horizontal Scaling

### Stateless Services
- No local state (sessions in Redis, not memory)
- Configuration from environment or config service
- Behind load balancer (round-robin, least-connections, or weighted)
- Auto-scaling based on CPU, memory, or custom metrics

### Load Balancing
- Layer 4 (TCP/UDP): fastest, no content inspection
- Layer 7 (HTTP): content-based routing, SSL termination, health checks
- Health check interval: 10 seconds, unhealthy threshold: 3, healthy threshold: 2

### Sticky Sessions
- Avoid when possible (breaks horizontal scaling)
- Only when: WebSocket connections, in-memory sessions, stateful protocols
- If required: use cookie-based affinity, not IP-based

## Circuit Breaker Pattern

```typescript
const circuit = new CircuitBreaker(async (url: string) => {
  return fetch(url, { signal: AbortSignal.timeout(5000) });
}, {
  failureThreshold: 5,        // Open after 5 failures
  resetTimeout: 30000,        // Try again after 30 seconds
  halfOpenMaxCalls: 3,        // Test with 3 calls before closing
});

circuit.on('open', () => {
  logger.warn('Circuit opened for payment service');
});

circuit.on('close', () => {
  logger.info('Circuit closed for payment service');
});

// Usage
try {
  const response = await circuit.call('https://payment-api/charge');
} catch (error) {
  if (error instanceof CircuitOpenError) {
    // Service is down, use fallback
    return queuePaymentForLater();
  }
  throw error;
}
```

### States
- **Closed**: Normal operation, requests pass through
- **Open**: Service is down, requests fail fast (no waiting for timeout)
- **Half-Open**: Testing recovery, limited requests pass through

## Rate Limiting

### Algorithms

#### Token Bucket
- Tokens added at fixed rate
- Each request consumes a token
- Burst allowed up to bucket size
- Good for: API rate limiting with burst allowance

#### Sliding Window
- Track requests in time window
- Reject when limit exceeded
- Good for: strict rate limiting

#### Fixed Window Counter
- Simple counter per time window
- Reset at window boundary
- Good for: simple limits, but allows 2x burst at boundaries

### Implementation (Redis)
```typescript
async function checkRateLimit(key: string, limit: number, window: number): Promise<boolean> {
  const current = await redis.incr(key);
  if (current === 1) {
    await redis.expire(key, window);
  }
  return current <= limit;
}

// Usage: 100 requests per minute per user
const allowed = await checkRateLimit(`ratelimit:user:${userId}`, 100, 60);
if (!allowed) {
  return res.status(429).json({ error: 'Rate limit exceeded' });
}
```

## Performance Monitoring

### Application Metrics
- Request rate (requests/second)
- Error rate (errors/second, error percentage)
- Latency (p50, p95, p99)
- Throughput (bytes/second, records/second)
- Active connections
- Queue depth

### Infrastructure Metrics
- CPU utilization
- Memory usage (RSS, heap, GC pressure)
- Disk I/O (read/write IOPS, latency)
- Network I/O (bandwidth, packet loss)

### Business Metrics
- Active users
- Conversion rate
- Revenue per request
- Cost per request

### RED Method (for Services)
- **Rate**: requests per second
- **Errors**: failed requests per second
- **Duration**: distribution of request times

### USE Method (for Resources)
- **Utilization**: percentage of time resource is busy
- **Saturation**: queue length (work waiting)
- **Errors**: error count

## Scaling Checklist

- [ ] Database queries optimized (no N+1, proper indexes)
- [ ] Connection pooling configured
- [ ] Caching strategy implemented (L1, L2, L3)
- [ ] Async processing for heavy operations
- [ ] Rate limiting on public endpoints
- [ ] Circuit breakers for external dependencies
- [ ] Horizontal scaling tested (load balancer, auto-scaling)
- [ ] CDN configured for static assets and cacheable responses
- [ ] Monitoring and alerting in place (RED method)
- [ ] Load testing performed before production deployment
- [ ] Database read replicas configured for read-heavy workloads
- [ ] Message queues for decoupled processing
