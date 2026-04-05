# Deployment & Operations Reference

## CI/CD Pipeline

### Pipeline Stages
```
Lint → Type Check → Unit Tests → Integration Tests → Build → Security Scan → Deploy
  │        │            │              │             │         │            │
  └─ Fail ─┴───── Fail ──┴──────── Fail ─┴────── Fail ─┴─── Fail ─┴──── Fail ─┘
```

### Rules
- Every stage gates the next. Failure blocks progression.
- Fast stages first (lint, type check). Slow stages last (integration, deploy).
- Parallelize independent stages (lint + type check + unit tests).
- Cache dependencies between runs.
- Artifacts from build stage are immutable and versioned.

### Deployment Strategies

#### Blue-Green Deployment
```
1. Deploy new version to green environment (idle)
2. Run health checks on green
3. Switch load balancer from blue to green
4. Green is now production
5. Blue is idle (keep for rollback)
6. If issues: switch back to blue (instant rollback)
```
- Zero downtime
- Instant rollback
- Requires 2x infrastructure cost

#### Canary Deployment
```
1. Deploy new version to small percentage of traffic (5%)
2. Monitor error rates, latency, business metrics
3. Gradually increase: 5% → 25% → 50% → 100%
4. If metrics degrade: automatic rollback
```
- Detects issues before full rollout
- Lower infrastructure cost than blue-green
- Requires traffic splitting capability

#### Rolling Deployment
```
1. Replace instances one at a time (or in batches)
2. Wait for health check to pass before replacing next
3. Old and new versions coexist during deployment
```
- No extra infrastructure cost
- Slower than blue-green
- Must handle version compatibility during rollout

## Infrastructure as Code

### Terraform Structure
```
infrastructure/
├── main.tf              # Provider configuration
├── variables.tf         # Input variables
├── outputs.tf           # Output values
├── modules/
│   ├── vpc/
│   ├── ecs/
│   ├── rds/
│   └── cloudfront/
├── environments/
│   ├── dev/
│   ├── staging/
│   └── production/
└── state/               # Remote state backend
```

### Rules
- No manual console configuration. Everything in code.
- State stored remotely (S3 + DynamoDB for locking, Terraform Cloud).
- State is encrypted at rest.
- Plan before apply. Review plan in CI.
- Use modules for reusable infrastructure patterns.
- Tag all resources: environment, team, cost-center, application.

## Health Checks

### Liveness Probe
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10
  timeoutSeconds: 3
  failureThreshold: 3
```
- "Is the process alive?"
- If fails: restart the container
- Should be simple: can the process respond?

### Readiness Probe
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
  timeoutSeconds: 3
  failureThreshold: 3
```
- "Can the service handle traffic?"
- If fails: remove from load balancer
- Check dependencies: database connection, cache connection

### Startup Probe
```yaml
startupProbe:
  httpGet:
    path: /healthz
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```
- "Is initialization complete?"
- Used for slow-starting applications
- Disables liveness probe until it succeeds

## Monitoring

### Three Pillars of Observability

#### Metrics (Prometheus)
```typescript
// Custom application metrics
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration in seconds',
  labelNames: ['method', 'route', 'status'],
  buckets: [0.01, 0.05, 0.1, 0.5, 1, 5],
});

const activeConnections = new Gauge({
  name: 'db_active_connections',
  help: 'Number of active database connections',
});
```

Key metrics to track:
- HTTP request rate, error rate, duration
- Database query latency, connection pool utilization
- Cache hit rate, eviction rate
- Queue depth, processing latency
- Memory usage, GC pause times
- CPU utilization

#### Logs (Structured JSON)
```json
{
  "timestamp": "2026-01-15T10:30:00.000Z",
  "level": "error",
  "service": "user-service",
  "correlationId": "req-abc123",
  "message": "Failed to create user",
  "error": "Duplicate key violation",
  "userId": "usr-456",
  "duration_ms": 45
}
```

Rules:
- Structured format (JSON) for machine parsing
- Correlation ID traces request across services
- Never log secrets, tokens, passwords, PII
- Include context: service, environment, version
- Centralized log aggregation (ELK, Datadog, CloudWatch)

#### Traces (OpenTelemetry)
```typescript
import { trace } from '@opentelemetry/api';

const tracer = trace.getTracer('user-service');

async function createUser(data: UserData) {
  return tracer.startActiveSpan('createUser', async (span) => {
    span.setAttribute('user.email', data.email);
    try {
      const user = await db.users.create(data);
      span.setStatus({ code: SpanStatusCode.OK });
      return user;
    } catch (error) {
      span.setStatus({ code: SpanStatusCode.ERROR });
      span.recordException(error);
      throw error;
    } finally {
      span.end();
    }
  });
}
```

## Alerting

### Alert on Symptoms, Not Causes
| Good Alert | Bad Alert |
|-----------|----------|
| API error rate > 1% | CPU > 80% |
| p99 latency > 1s | Memory > 70% |
| Payment failures increasing | Disk IOPS high |
| User signup rate dropped | Network bandwidth high |

### Alert Severity Levels
| Level | Response | Example |
|-------|----------|---------|
| P0 - Critical | Page immediately, 24/7 | Service down, data loss, security breach |
| P1 - High | Page during business hours | Elevated error rate, degraded performance |
| P2 - Medium | Ticket, next business day | Warning thresholds approaching |
| P3 - Low | Backlog, investigate when able | Informational, trend analysis |

### Alert Rules
- Every alert must be actionable
- Every alert must have a runbook
- No alert fatigue: if it pages, it matters
- Alert fatigue is worse than no alerting
- Review and tune alerts monthly

## Incident Response

### Incident Lifecycle
1. **Detect**: Monitoring triggers alert
2. **Acknowledge**: On-call engineer acknowledges
3. **Assess**: Determine severity and scope
4. **Mitigate**: Restore service (fix comes later)
5. **Resolve**: Root cause identified and fixed
6. **Post-mortem**: Document what happened, why, and prevention

### Post-Mortem Template
```
## Incident Summary
- **Date**: 2026-01-15
- **Duration**: 45 minutes
- **Severity**: P1
- **Impact**: 15% of users experienced 500 errors

## Timeline
- 10:30 - Alert triggered: error rate > 5%
- 10:32 - On-call acknowledged
- 10:35 - Root cause identified: database connection pool exhausted
- 10:40 - Mitigation: increased pool size, restarted services
- 10:50 - Service restored
- 11:15 - All metrics back to normal

## Root Cause
Database connection pool was set to 50, but traffic spike required 200+ connections.
New feature deployed at 10:00 increased average query duration from 10ms to 100ms,
causing connections to be held longer.

## Action Items
- [ ] Increase default connection pool to 200 (Owner: @dev1, Due: 2026-01-20)
- [ ] Add alert for connection pool utilization > 70% (Owner: @dev2, Due: 2026-01-22)
- [ ] Optimize slow query identified in feature X (Owner: @dev3, Due: 2026-01-25)
- [ ] Add load test for new feature before deployment (Owner: @dev1, Due: 2026-02-01)
```

## Feature Flags

### Implementation
```typescript
const flags = {
  'new-checkout-flow': {
    enabled: true,
    rollout: 0.5,        // 50% of users
    targeting: {
      userIds: ['usr-123', 'usr-456'],  // Always enabled for these
      excludeUserIds: ['usr-789'],       // Always disabled for these
    },
  },
};

function isFeatureEnabled(flag: string, context: FeatureContext): boolean {
  const config = flags[flag];
  if (!config?.enabled) return false;
  if (config.targeting.userIds?.includes(context.userId)) return true;
  if (config.targeting.excludeUserIds?.includes(context.userId)) return false;
  return hash(context.userId) < config.rollout;
}
```

### Rules
- Decouple deployment from release
- Every new feature behind a flag
- Flags have owners and expiration dates
- Remove flags after feature is fully rolled out
- Flag cleanup is part of definition of done

## Disaster Recovery

### RTO (Recovery Time Objective)
- How quickly can we restore service?
- Target: < 1 hour for critical services

### RPO (Recovery Point Objective)
- How much data can we afford to lose?
- Target: < 15 minutes for critical data

### DR Plan
1. **Automated failover**: Primary region fails, traffic routes to secondary
2. **Database replication**: Cross-region read replicas, promote on failover
3. **DNS failover**: Route53 health checks with failover routing
4. **Regular DR drills**: Test failover quarterly
5. **Documented runbook**: Step-by-step recovery procedure

### Backup Verification
- Monthly: restore latest backup to staging environment
- Quarterly: full DR drill (failover to secondary region)
- Annually: complete infrastructure rebuild from IaC

## Deployment Checklist

- [ ] CI pipeline passes (lint, test, build, scan)
- [ ] Database migrations tested and reversible
- [ ] Feature flags configured for new features
- [ ] Health checks defined and passing
- [ ] Monitoring dashboards updated
- [ ] Alert thresholds reviewed
- [ ] Rollback plan documented
- [ ] Load testing performed (if significant changes)
- [ ] Security scan passed (no critical/high vulnerabilities)
- [ ] Dependencies reviewed and pinned
- [ ] Environment variables configured for target environment
- [ ] Secrets rotated (if scheduled rotation)
