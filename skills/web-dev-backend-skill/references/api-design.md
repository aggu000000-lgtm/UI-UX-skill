# API Design Reference

## REST API Standards

### Resource Naming

- Use plural nouns for collections: `/users`, `/orders`, `/products`
- Use singular nouns for single resources: `/users/{id}`, `/orders/{orderId}`
- Use sub-resources for relationships: `/users/{id}/orders`, `/orders/{id}/items`
- Avoid verbs in URLs. HTTP methods carry action semantics.
- Use kebab-case for multi-word resources: `/api-keys`, `/user-profiles`
- Version in URL path: `/v1/users` or via Accept header: `Accept: application/vnd.api.v1+json`

### HTTP Methods and Semantics

| Method | Safe | Idempotent | Purpose |
|--------|------|------------|---------|
| GET | Yes | Yes | Retrieve resource(s) |
| POST | No | No | Create resource or trigger action |
| PUT | No | Yes | Full resource replacement |
| PATCH | No | No (usually) | Partial resource update |
| DELETE | No | Yes | Remove resource |
| HEAD | Yes | Yes | Retrieve headers only |
| OPTIONS | Yes | Yes | Discover supported methods |

### Status Code Usage

#### Success (2xx)
- `200 OK` — Standard success response
- `201 Created` — Resource successfully created (include `Location` header)
- `202 Accepted` — Request accepted for async processing
- `204 No Content` — Success with no response body (DELETE, PUT)

#### Client Errors (4xx)
- `400 Bad Request` — Malformed request, invalid JSON, missing required fields
- `401 Unauthorized` — Missing or invalid authentication
- `403 Forbidden` — Authenticated but lacks permission
- `404 Not Found` — Resource does not exist
- `405 Method Not Allowed` — HTTP method not supported for this resource
- `409 Conflict` — Request conflicts with current state (duplicate resource)
- `422 Unprocessable Entity` — Valid syntax but semantic errors (validation failures)
- `429 Too Many Requests` — Rate limit exceeded

#### Server Errors (5xx)
- `500 Internal Server Error` — Unexpected server failure
- `502 Bad Gateway` — Invalid response from upstream
- `503 Service Unavailable` — Temporarily unable to handle request
- `504 Gateway Timeout` — Upstream did not respond in time

### Response Envelope

```json
{
  "data": { },
  "meta": {
    "page": 1,
    "per_page": 20,
    "total": 150
  },
  "errors": [ ]
}
```

- `data`: Primary response payload (null when errors occur)
- `meta`: Pagination, timing, or contextual metadata
- `errors`: Array of error objects (present only on error)

Error object format:
```json
{
  "code": "VALIDATION_ERROR",
  "field": "email",
  "message": "Email address is already in use",
  "details": { }
}
```

### Pagination

#### Cursor-Based (Preferred for Large Datasets)
```
GET /v1/users?limit=20&cursor=eyJpZCI6MTAwfQ
```
Response:
```json
{
  "data": [ ],
  "meta": {
    "next_cursor": "eyJpZCI6MTIwfQ",
    "has_more": true
  }
}
```

#### Offset-Based (Small Datasets, Admin Panels)
```
GET /v1/users?limit=20&offset=40
```
Response:
```json
{
  "data": [ ],
  "meta": {
    "total": 150,
    "page": 3,
    "per_page": 20,
    "total_pages": 8
  }
}
```

### Filtering, Sorting, Field Selection

```
GET /v1/users?status=active&role=admin&sort=-created_at&fields=id,name,email
```

- Filter: query parameters with field names
- Sort: prefix with `-` for descending, no prefix for ascending
- Fields: comma-separated list for sparse fieldsets
- Search: dedicated `q` parameter for full-text search

### Idempotency

For POST and PATCH operations that modify state:

```
POST /v1/payments
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
```

- Server stores idempotency key + response for 24 hours
- Duplicate key returns cached response
- Key expires after TTL to prevent unbounded storage
- Required for payment processing, order creation, any operation with side effects

### Rate Limiting

Response headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 847
X-RateLimit-Reset: 1704067200
Retry-After: 60
```

- Default: 1000 requests/hour for authenticated, 100/hour for unauthenticated
- Sensitive endpoints (auth, payments): stricter limits
- Return `429 Too Many Requests` when exceeded
- Include `Retry-After` header with seconds to wait

### Error Handling

#### Validation Errors (422)
```json
{
  "data": null,
  "errors": [
    {
      "code": "VALIDATION_ERROR",
      "field": "email",
      "message": "Must be a valid email address"
    },
    {
      "code": "VALIDATION_ERROR",
      "field": "password",
      "message": "Must be at least 8 characters"
    }
  ]
}
```

#### Not Found (404)
```json
{
  "data": null,
  "errors": [
    {
      "code": "NOT_FOUND",
      "message": "User with ID 'abc123' not found"
    }
  ]
}
```

#### Server Error (500)
```json
{
  "data": null,
  "errors": [
    {
      "code": "INTERNAL_ERROR",
      "message": "An unexpected error occurred. Please try again later."
    }
  ]
}
```
- Never expose stack traces, query details, or internal paths
- Log full error details server-side with correlation ID
- Return user-safe message to client

## GraphQL Standards

### Schema Design
- Types map to domain entities
- Queries for reads, Mutations for writes, Subscriptions for real-time
- Use input types for mutation arguments
- Implement Relay-style cursor pagination for connections

### Security Controls
- Query depth limit: max 10 levels
- Query complexity analysis: assign cost to fields, reject queries exceeding threshold
- Persisted queries: whitelist known queries in production
- DataLoader: batch and cache resolver calls to prevent N+1

### Response Format
```json
{
  "data": { },
  "errors": [
    {
      "message": "Field 'email' requires authentication",
      "path": ["user", "email"],
      "extensions": {
        "code": "UNAUTHENTICATED"
      }
    }
  ]
}
```

## gRPC Standards

### When to Use
- Internal service-to-service communication
- Low-latency, high-throughput requirements
- Strong typing with protobuf contracts
- Streaming use cases (bidirectional, server-streaming, client-streaming)

### Contract Design
- Protobuf files versioned alongside service code
- Backward compatibility: new fields are optional, never change field numbers
- Use `oneof` for mutually exclusive fields
- Document all messages and services with comments

### Error Handling
- Use standard gRPC status codes
- Return error details in `google.rpc.Status`
- Map gRPC errors to HTTP status codes at the API gateway

## API Versioning Strategies

### URL Path Versioning (Most Common)
```
/v1/users
/v2/users
```
- Clear, cacheable, easy to understand
- Old versions coexist with new

### Header Versioning
```
Accept: application/vnd.api.v2+json
```
- Clean URLs
- Harder to test and cache

### Deprecation Policy
- Minimum 12 months notice before sunsetting a version
- `Sunset` header with deprecation date
- `Deprecation` header on deprecated endpoints
- Migration guide for each version bump
- Monitor usage before deprecating

## Webhook Design

### Delivery
- POST to registered URL with event payload
- Include event type, timestamp, and unique event ID
- Retry with exponential backoff: 1s, 5s, 30s, 5min, 30min, 1hr, 6hr, 12hr, 24hr
- Max retries: 25 attempts over 72 hours

### Security
- HMAC signature in `X-Webhook-Signature` header
- Secret per webhook endpoint
- Timestamp in payload to prevent replay attacks (reject if > 5 minutes old)

### Payload
```json
{
  "id": "evt_123456",
  "type": "user.created",
  "timestamp": "2026-01-15T10:30:00Z",
  "data": {
    "id": "usr_789",
    "email": "user@example.com"
  }
}
```

## OpenAPI Specification

- Generate from code, do not hand-write
- Keep spec in sync via CI validation
- Include examples for all request/response schemas
- Document all error responses
- Use `$ref` for shared schemas
- Tag operations by resource group
- Include security scheme definitions
