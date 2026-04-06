# Database Architecture Reference

## Database Selection Matrix

| Data Pattern | Recommended Database | Examples |
|-------------|---------------------|----------|
| Structured, transactional, ACID required | Relational (PostgreSQL, MySQL) | Users, orders, payments, inventory |
| Flexible schema, document-oriented | Document (MongoDB, Couchbase) | Content management, catalogs, user profiles |
| Highly connected data, graph traversals | Graph (Neo4j, Amazon Neptune) | Social networks, recommendations, fraud detection |
| Time-ordered events, metrics | Time-Series (TimescaleDB, InfluxDB) | IoT sensor data, monitoring, financial ticks |
| Analytical queries, aggregations | Columnar (ClickHouse, Redshift) | BI dashboards, data warehouses, log analytics |
| Key-value lookups, caching | Key-Value (Redis, DynamoDB) | Sessions, caches, feature flags |
| Full-text search | Search Engine (Elasticsearch, Meilisearch) | Product search, log search, document search |

## Schema Design Principles

### Normalization
- **1NF**: Atomic values, no repeating groups
- **2NF**: No partial dependencies on composite keys
- **3NF**: No transitive dependencies (non-key depends on non-key)
- Normalize to 3NF by default. Denormalize only when:
  - Query patterns require frequent joins that impact performance
  - Read-to-write ratio exceeds 100:1
  - Document the denormalization and the query pattern that justifies it

### Table Design
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'suspended', 'deleted')),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);
```

- Every table has a primary key (UUIDv7 or auto-increment integer)
- `created_at` and `updated_at` on every table
- `deleted_at` for soft deletes (set to NULL, populate on delete)
- CHECK constraints for enumerated values
- NOT NULL on all required columns (avoid NULL unless semantically meaningful)
- Foreign keys with explicit ON DELETE/ON UPDATE behavior

### Relationship Patterns

#### One-to-Many
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    total_cents INTEGER NOT NULL CHECK (total_cents >= 0),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

#### Many-to-Many (Junction Table)
```sql
CREATE TABLE order_items (
    order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE RESTRICT,
    quantity INTEGER NOT NULL CHECK (quantity > 0),
    price_cents INTEGER NOT NULL CHECK (price_cents >= 0),
    PRIMARY KEY (order_id, product_id)
);
```

#### Self-Referencing
```sql
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    parent_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    name VARCHAR(100) NOT NULL,
    path LTREE NOT NULL
);
```

## Indexing Strategy

### Index Types
- **B-Tree** (default): Equality and range queries. Works for most cases.
- **Hash**: Equality only. Faster than B-Tree for exact matches (PostgreSQL).
- **GIN**: Full-text search, JSONB, array containment.
- **GiST**: Geospatial, range types, full-text search.
- **BRIN**: Large tables with natural ordering (time-series data).

### Index Rules
- Index foreign key columns (prevents full table scans on joins)
- Index columns used in WHERE, ORDER BY, JOIN, and GROUP BY
- Composite indexes: order columns by selectivity (most selective first)
- Covering indexes (INCLUDE) to avoid table lookups for frequently accessed columns
- Partial indexes for filtered queries: `WHERE status = 'active'`
- Monitor index usage. Unused indexes slow writes and waste storage.

### Verification
```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'user@example.com';
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 'uuid' AND status = 'pending';
```

- Look for `Seq Scan` on large tables — indicates missing index
- Look for `Index Scan` or `Index Only Scan` — good
- Check `rows` estimate vs `actual rows` — large discrepancy means stale statistics

## Migration Management

### Migration File Naming
```
001_create_users.up.sql
001_create_users.down.sql
002_create_orders.up.sql
002_create_orders.down.sql
```

### Rules
- Every migration must have a reversible down migration
- Test up and down migrations locally before production
- Never modify an existing migration file — create a new one
- Deploy migrations before application code (backward compatible changes)
- For breaking changes: deploy in phases
  1. Add new column (nullable)
  2. Deploy code that writes to both old and new
  3. Backfill data
  4. Deploy code that reads from new column
  5. Remove old column

### Zero-Downtime Migration Example
```sql
-- Phase 1: Add column (safe, non-blocking)
ALTER TABLE users ADD COLUMN email_normalized VARCHAR(255);

-- Phase 3: Backfill (run in batches to avoid lock)
UPDATE users SET email_normalized = LOWER(email)
WHERE id BETWEEN 1 AND 10000;

-- Phase 5: Remove old column (after confirming no code uses it)
ALTER TABLE users DROP COLUMN email;
```

## Replication

### Primary-Replica Setup
- Write to primary, read from replicas
- Handle replication lag:
  - Read-your-writes consistency: route reads to primary after a write from the same session
  - Stale reads acceptable for analytics, dashboards, search results
- Monitor replication lag: alert if > 5 seconds

### Read Routing
```
Write:  POST /users  -> primary
Read:   GET /users/1 -> replica (if lag < threshold, else primary)
```

## Connection Pooling

### Why
- Database connections are expensive (memory, file descriptors)
- Opening/closing per request adds latency
- Connection limits are finite (PostgreSQL default: 100)

### Implementation
- PgBouncer (PostgreSQL), ProxySQL (MySQL), or cloud-native (RDS Proxy, Cloud SQL Proxy)
- Pool size: `(core_count * 2) + effective_spindle_count`
- Monitor: active connections, waiting connections, connection errors

## Backup Strategy

### Types
- **Full backup**: Complete database snapshot. Weekly.
- **Incremental backup**: Changes since last backup. Daily.
- **WAL archiving** (PostgreSQL): Point-in-time recovery. Continuous.

### Rules
- Automated backups (never manual)
- Test restoration quarterly (a backup not restored is not a backup)
- Store backups in a different region from primary
- Encrypt backups at rest and in transit
- Retention: 30 days daily, 12 months monthly, 7 years yearly (compliance)

### Recovery Time Objectives
| Database Size | RTO Target | RPO Target |
|--------------|-----------|-----------|
| < 10 GB | 15 minutes | 5 minutes |
| 10-100 GB | 30 minutes | 15 minutes |
| 100 GB - 1 TB | 1 hour | 30 minutes |
| > 1 TB | 2 hours | 1 hour |

## Query Optimization

### Common Anti-Patterns
- `SELECT *` — fetch only needed columns
- N+1 queries — use JOINs, eager loading, or batch queries
- Unbounded queries — always use LIMIT for user-facing queries
- Functions on indexed columns — `WHERE LOWER(email)` bypasses index. Use functional index or store normalized value.
- Implicit type conversion — `WHERE user_id = '123'` when user_id is integer

### Pagination at Scale
```sql
-- Bad: OFFSET gets slower with larger offsets
SELECT * FROM orders ORDER BY created_at DESC LIMIT 20 OFFSET 100000;

-- Good: cursor-based, constant time
SELECT * FROM orders
WHERE created_at < '2026-01-15T10:30:00Z'
ORDER BY created_at DESC
LIMIT 20;
```

## Database Monitoring

### Key Metrics
- Query latency (p50, p95, p99)
- Connection pool utilization
- Replication lag
- Cache hit ratio (should be > 95%)
- Slow query count (> 1 second)
- Deadlock count
- Disk usage and growth rate

### Alerts
- Query latency p99 > 1 second
- Connection pool > 80% utilized
- Replication lag > 5 seconds
- Cache hit ratio < 90%
- Disk usage > 80%
