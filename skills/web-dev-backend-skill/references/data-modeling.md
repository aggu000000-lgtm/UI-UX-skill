# Data Modeling Reference

## Entity Design Principles

### Naming
- Use clear, domain-specific names: `User`, `Order`, `Payment`, not `tbl_usr`, `ord`, `pmt`
- Singular for table names (controversial, but consistent with ORM conventions)
- Avoid abbreviations unless universally understood (ID, URL, IP)
- Avoid reserved words: `user`, `order`, `group`, `key`, `index`

### Primary Keys

#### UUIDv7 (Preferred)
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    -- ...
);
```
- Time-ordered (better index performance than random UUIDs)
- Globally unique (no coordination needed)
- Unpredictable (security benefit over sequential IDs)
- 16 bytes (larger than integer, but worth it)

#### Auto-Increment Integer
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    -- ...
);
```
- Smaller storage footprint (4-8 bytes)
- Faster index lookups
- Predictable (information leakage risk)
- Coordination required (single writer bottleneck)

### Natural Keys as Constraints
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    email VARCHAR(255) NOT NULL UNIQUE,  -- Natural key
    -- ...
);
```

## Relationship Patterns

### One-to-One
```sql
CREATE TABLE user_profiles (
    user_id UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
    bio TEXT,
    avatar_url VARCHAR(500),
    -- ...
);
```
- Use when: data has different access patterns, lifecycle, or security requirements
- Example: user credentials vs. user profile (different read/write patterns)

### One-to-Many
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    -- ...
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
```
- Most common relationship pattern
- Always index the foreign key column
- ON DELETE CASCADE: child records deleted with parent
- ON DELETE RESTRICT: prevent deletion if children exist
- ON DELETE SET NULL: orphan child records (use carefully)

### Many-to-Many
```sql
CREATE TABLE order_items (
    order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE RESTRICT,
    quantity INTEGER NOT NULL CHECK (quantity > 0),
    price_cents INTEGER NOT NULL CHECK (price_cents >= 0),
    PRIMARY KEY (order_id, product_id)
);
```
- Junction table with composite primary key
- Include relationship-specific data (quantity, price_at_time)
- Index both foreign keys

### Self-Referencing
```sql
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    parent_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    name VARCHAR(100) NOT NULL,
    -- ...
);
```
- Use for: hierarchies, trees, graphs
- Consider: materialized path (ltree), nested sets, or closure tables for deep trees

## Audit Fields

### Minimum
```sql
CREATE TABLE users (
    -- ...
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
);
```

### Extended (When Accountability Matters)
```sql
CREATE TABLE orders (
    -- ...
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    created_by UUID REFERENCES users(id),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_by UUID REFERENCES users(id),
    deleted_at TIMESTAMPTZ,
    deleted_by UUID REFERENCES users(id),
);
```

### Automatic Updated At
```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_users_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

## Enumerations

### Database-Level (PostgreSQL)
```sql
CREATE TYPE user_status AS ENUM ('pending', 'active', 'suspended', 'deleted');

CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    status user_status NOT NULL DEFAULT 'pending',
    -- ...
);
```

### CHECK Constraint (Portable)
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    status VARCHAR(20) NOT NULL DEFAULT 'pending'
        CHECK (status IN ('pending', 'active', 'suspended', 'deleted')),
    -- ...
);
```

### Rules
- Application-level enums must match database constraints
- Adding values is safe. Removing values requires migration.
- Document the meaning of each value.

## Soft Deletes

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    -- ...
    deleted_at TIMESTAMPTZ,
);

-- Query active records
SELECT * FROM users WHERE deleted_at IS NULL;

-- Unique constraint with soft deletes
CREATE UNIQUE INDEX idx_users_email_active
    ON users(email) WHERE deleted_at IS NULL;
```

### Rules
- Soft delete for user-facing data (audit trail, legal compliance)
- Hard delete for temporary data, caches, logs past retention
- Exclude soft-deleted records from unique constraints (partial index)
- Consider data retention policies (GDPR right to erasure)

## Polymorphic Associations (Anti-Pattern)

```sql
-- BAD: Polymorphic association
CREATE TABLE comments (
    id UUID PRIMARY KEY,
    body TEXT NOT NULL,
    commentable_id UUID NOT NULL,
    commentable_type VARCHAR(50) NOT NULL,  -- 'post', 'video', 'product'
);
```

### Why It's Bad
- No foreign key integrity
- No cascading deletes
- Complex queries (UNION across tables)
- No type safety

### Better: Separate Tables
```sql
CREATE TABLE post_comments (
    id UUID PRIMARY KEY,
    post_id UUID NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    body TEXT NOT NULL,
);

CREATE TABLE video_comments (
    id UUID PRIMARY KEY,
    video_id UUID NOT NULL REFERENCES videos(id) ON DELETE CASCADE,
    body TEXT NOT NULL,
);
```

### Or: Single Table with Nullable Foreign Keys
```sql
CREATE TABLE comments (
    id UUID PRIMARY KEY,
    post_id UUID REFERENCES posts(id) ON DELETE CASCADE,
    video_id UUID REFERENCES videos(id) ON DELETE CASCADE,
    body TEXT NOT NULL,
    CHECK (
        (post_id IS NOT NULL AND video_id IS NULL) OR
        (post_id IS NULL AND video_id IS NOT NULL)
    )
);
```

## Event Sourcing

### When to Use
- Complete audit trail is required
- State reconstruction from history is valuable
- Temporal queries ("what was the state at time X?")
- Complex domain with many state transitions

### Event Store
```sql
CREATE TABLE events (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    aggregate_id UUID NOT NULL,
    aggregate_type VARCHAR(50) NOT NULL,
    event_type VARCHAR(100) NOT NULL,
    version INTEGER NOT NULL,
    data JSONB NOT NULL,
    metadata JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (aggregate_id, aggregate_type, version)
);

CREATE INDEX idx_events_aggregate ON events(aggregate_id, aggregate_type);
```

### Projection
```sql
-- Current state derived from events
CREATE MATERIALIZED VIEW user_current_state AS
SELECT
    aggregate_id AS user_id,
    (data->>'email')::TEXT AS email,
    (data->>'name')::TEXT AS name,
    (data->>'status')::TEXT AS status,
    MAX(created_at) AS last_updated
FROM events
WHERE aggregate_type = 'user'
GROUP BY aggregate_id, data;
```

### Rules
- Events are immutable (append-only)
- Events describe what happened, not what should happen
- Event schema evolves: new event types, never change existing events
- Projections can be rebuilt from events
- Snapshots for performance (rebuild from snapshot + subsequent events)

## CQRS (Command Query Responsibility Segregation)

### When to Use
- Read and write models have fundamentally different shapes
- Read and write scales differently
- Complex queries that don't match the write model

### Write Model (Command Side)
```sql
-- Normalized, transactional, ACID
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v7(),
    user_id UUID NOT NULL REFERENCES users(id),
    status VARCHAR(20) NOT NULL DEFAULT 'pending',
    total_cents INTEGER NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
);
```

### Read Model (Query Side)
```sql
-- Denormalized, optimized for queries
CREATE MATERIALIZED VIEW order_details AS
SELECT
    o.id,
    o.status,
    o.total_cents,
    o.created_at,
    u.email AS user_email,
    u.name AS user_name,
    COUNT(oi.id) AS item_count,
    ARRAY_AGG(p.name) AS product_names
FROM orders o
JOIN users u ON o.user_id = u.id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
GROUP BY o.id, o.status, o.total_cents, o.created_at, u.email, u.name;
```

### Rules
- CQRS is not the default. Start with a single model.
- Introduce CQRS when read/write divergence is proven, not anticipated.
- Eventual consistency between write and read models is acceptable.
- Keep the synchronization mechanism simple (database triggers, event handlers).

## Data Modeling Checklist

- [ ] Entity names are clear and domain-specific
- [ ] Primary keys are UUIDv7 or auto-increment
- [ ] Foreign keys have explicit ON DELETE/ON UPDATE behavior
- [ ] Foreign key columns are indexed
- [ ] Audit fields (created_at, updated_at) on every table
- [ ] CHECK constraints for enumerated values
- [ ] NOT NULL on all required columns
- [ ] Soft deletes for user-facing data
- [ ] No polymorphic associations
- [ ] Unique constraints for natural keys
- [ ] Composite indexes match query patterns
- [ ] Database enums match application enums
- [ ] Migrations are reversible and tested
