# Non-Functional Requirements Specifications

**Purpose:** Define measurable, testable non-functional requirements for all features.

**Version:** 1.0  
**Last Updated:** 2026-04-07  
**Alignment:** Constitution guidelines

---

## Overview

Non-Functional Requirements (NFRs) define HOW the system performs, not WHAT it does.

Categories:
- **Performance** - Speed, throughput, latency
- **Security** - Authentication, authorization, encryption
- **Reliability** - Availability, fault tolerance, recovery
- **Scalability** - Handling growth, resource efficiency
- **Maintainability** - Code quality, documentation
- **Compliance** - Regulations, audit trails

---

## Template: Feature NFRs

```markdown
# NFR Specifications: [Feature Name]

**Related Specification:** `/.spec/specs/{feature-name}.md`  
**Related Architecture:** `/.spec/reference-architecture.md`  
**Baseline:** Constitution guidelines in `/.spec/constitution.md`

## Performance Requirements

### Response Time SLAs

| Operation | P50 | P95 | P99 | Rationale |
|---|---|---|---|---|
| [Operation name] | [Target]ms | [Target]ms | [Target]ms | [Why this target] |

### Throughput Targets

- **Sustained Throughput:** [X] requests/second per instance
- **Burst Capacity:** [X] requests/second (for [duration])
- **Connection Pool:** [X] connections per instance

### Resource Efficiency

- **Heap Memory:** ≤ [X]MB baseline
- **CPU Utilization:** Target [X%] under normal load
- **Disk Usage:** [X]GB per [period]
- **Network Bandwidth:** [X]Mbps peak

## Security Requirements

### Authentication
- [Specific requirements]

### Authorization
- [Specific requirements]

### Data Protection
- [Encryption standards]
- [Sensitive data handling]

## Reliability Requirements

### Availability
- **Target SLA:** [X]% uptime ([Y] minutes downtime/month)
- **MTBF:** > [X] hours
- **MTTR:** < [X] minutes

### Fault Tolerance
- [Failure scenarios and handling]

## Scalability Requirements

### Horizontal Scaling
- [How does it scale]
- [Stateless design requirements]

### Database Scaling
- [Indexing requirements]
- [Sharding/replication if needed]

## Compliance & Audit

### Audit Trail
- [What must be logged]
- [Retention period]

### Data Residency
- [Where data must be stored]
```

---

## Example: User Authentication NFRs

# NFR Specifications: User Authentication with JWT

**Related Specification:** `/.spec/specs/user-authentication.md`  
**Baseline:** Constitution performance requirements

## Performance Requirements

### Response Time SLAs

| Operation | P50 | P95 | P99 | Rationale |
|---|---|---|---|---|
| Login (credential validation) | 50ms | 100ms | 200ms | Fast credential check enables mobile UX |
| Token validation per request | 1ms | 5ms | 10ms | Minimal overhead for on every request |
| Token refresh | 30ms | 50ms | 100ms | Background operation, less critical |

### Throughput Targets

- **Sustained Throughput:** 1,000 requests/second per instance
- **Burst Capacity:** 5,000 requests/second (for 30 seconds)
- **Connection Pool:** 20 connections per instance (HikariCP)

### Resource Efficiency

- **Heap Memory:** ≤ 512MB baseline (JWT operations have minimal memory)
- **CPU Utilization:** Target 60-70% under normal load (bcrypt uses CPU)
- **Disk Usage:** Negligible (JWT tokens ephemeral)
- **Network Bandwidth:** ~1KB per request average

## Security Requirements

### Authentication

- **Password Requirements:**
  - Minimum 8 characters
  - Validation: Uppercase, lowercase, numbers recommended (not enforced in Phase 1)
  - Storage: bcrypt hashing (cost factor 12 minimum)
  - Never log or expose plaintext passwords

- **Rate Limiting:**
  - Max 5 failed login attempts per minute per IP
  - Account unlock: Manual by admin or after 30 minutes
  - Failed attempt tracking: In-memory cache (Redis if multi-instance)

- **Token Management:**
  - Algorithm: RS256 (RSA signatures, asymmetric)
  - Key Management:
    - Private key: Stored in Kubernetes secret
    - Public key: Distributed to all instances
    - Key rotation: Annually (versioning via `kid` claim)
  - Token Structure:
    - Header: Algorithm, key ID
    - Payload: user_id, roles, iat, exp
    - Signature: RS256 signature (tamper-proof)

- **Refresh Tokens:**
  - Lifetime: 7 days
  - Storage: Persisted in database (allowed refresh tokens list)
  - Revocation: Can be deleted from database immediately

### Authorization

- **Role-Based Access Control (RBAC):**
  - Roles stored in JWT payload for stateless validation
  - Endpoint protection: `@RolesAllowed("ROLE_ADMIN")`
  - Unauthorized access returns HTTP 403

### Data Protection

- **Transport Security:**
  - TLS 1.3 minimum (enforced by infrastructure)
  - HTTPS only, no HTTP fallback
  - HSTS headers enabled

- **Sensitive Data Handling:**
  - Never log tokens or passwords
  - Clear token from memory after use where possible
  - Database: password_hash never selected in query (column excluded)

---

## Reliability Requirements

### Availability

- **Target SLA:** 99.95% uptime (≈22 minutes downtime/month)
- **MTBF:** > 720 hours (month between failures)
- **MTTR:** < 5 minutes (recovery time)

### Fault Tolerance

**Scenario 1: Key Server Unavailable**
- Impact: Auth service still available (RSA public key cached in all servers)
- Mitigation: Stateless design, horizontal scaling

**Scenario 2: Database Unavailable**
- Impact: Token refresh fails, active tokens still valid
- MTTR: < 5 minutes (automatic failover to read replica)

**Scenario 3: Key Rotation Failure**
- Impact: Old keys still valid during transition period
- Mitigation: Support multiple keys simultaneously for 24-hour transition

---

## Scalability Requirements

### Horizontal Scaling

- **Stateless Design:** Each instance validates tokens independently
- **Load Balancing:** No session affinity required
- **Scaling Out:** Add more instances, no configuration needed

### Database Scaling

- **Read Replicas:** Database query for login + refresh token validation (read-only)
- **Indexing:**
  - PK: `users.id`
  - UK: `users.username` (login query)
  - FK: `refresh_tokens.user_id` (cleanup queries)
- **Connection Pool:** Min 10, Max 20 per instance

---

## Compliance & Audit

### Audit Trail

- **Login Events:** Log successful/failed login attempts
  - Data: username (not password!), timestamp, IP address, outcome
  - Retention: 90 days
  - Purpose: Security audit, breach investigation

- **Authorization Checks:** Log access to protected resources
  - Data: user_id, endpoint accessed, timestamp, result
  - Retention: 1 year
  - Purpose: Compliance, audit trail

### Monitoring & Alerting

- **Metrics to Track:**
  - Login success rate (alerts if < 95%)
  - Auth token validation latency (p95 > 10ms alert)
  - Failed login attempts (spike detection)

- **Health Checks:**
  - Readiness: Can generate JWT token
  - Liveness: TCP connection responds

---

## Testing Strategy

### Performance Tests

```bash
# Load test: 1000 concurrent users, login + verify token
ab -n 10000 -c 1000 -p credentials.json https://api.example.com/auth/login

# Latency test: Ensure P95 < 100ms
# Tool: Apache JMeter or similar
```

### Security Tests

```bash
# Password validation: Min 8 chars, bcrypt cost 12
# Test framework: JUnit (unit test)

# Token tampering: Modify token payload, verify rejection
# Test: AuthIntegrationTests

# Rate limiting: 6 attempts → 429 Too Many Requests
# Test: Load testing
```

---

## Verification Checklist (Before Release)

- [ ] P95 login response time measured < 100ms in load test
- [ ] P95 token validation overhead < 5ms in production simulation
- [ ] bcrypt cost factor verified as 12 in code review
- [ ] Rate limiting working (test with 6 attempts)
- [ ] Failed login attempts logged (security audit)
- [ ] Token refresh revocation working (JWT revocation list or refresh token delete)
- [ ] Multi-instance testing: Auth works across all instances
- [ ] Key rotation plan documented
- [ ] Disaster recovery tested (key loss scenario)

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Baseline:** Constitution `/.spec/constitution.md`
