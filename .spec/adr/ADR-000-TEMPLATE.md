# ADR-[NUMBER]: [Short Title]

**Date:** [YYYY-MM-DD]  
**Status:** Proposed | Accepted | Deprecated | Superseded  
**Authors:** [Name], [Name]  
**Related ADRs:** [Link to related ADRs if any]  

---

## Context

Describe the issue/problem that led to this decision.

Include:
- Why is this decision being made?
- What problem are we solving?
- What constraints do we have?
- What are the immediate requirements?
- What trends in technology, market, or user preferences are driving this?

**Example:**
```
We need to scale our authentication system to handle millions of concurrent users.
Current session-based approach requires shared session store, creating bottleneck.
Team wants stateless design for horizontal scaling.
We need to support multiple client types (web, mobile, third-party).
```

---

## Decision

State the decision clearly and concisely.

Use imperative language: "We will..." or "We have decided to..."

**Example:**
```
We will implement JWT-based authentication instead of session-based authentication.
```

---

## Rationale

Explain WHY this decision was chosen over alternatives.

Include:
- Technical advantages of this approach
- Business benefits
- Risk mitigation
- Team expertise
- Alignment with architecture principles

**Format: Explain the "why" clearly enough that someone 2 years from now can understand the reasoning.**

**Example:**
```
JWT enables stateless API design, eliminating server-side session store.
- Scales horizontally: each server can validate token independently
- Reduces infrastructure: no redis/memcached for sessions
- Standard industry pattern with mature libraries
- Works well with mobile and SPA architectures
- Aligns with microservices principles
```

---

## Consequences

Describe the results (positive and negative) of implementing this decision.

### Positive Consequences ✅
- [Benefit 1]: Explain the benefit
- [Benefit 2]: Explain the benefit

### Negative Consequences ⚠️
- [Issue 1]: Describe the trade-off
- [Issue 2]: Describe the trade-off

**Example:**
```
✅ Positive:
- Can handle unlimited concurrent users
- No session store bottleneck
- Easy to scale to multiple datacenters
- Simpler deployment (no session replication)

⚠️ Negative/Trade-offs:
- Cannot revoke tokens immediately (token lifetime must be short or add blacklist)
- Token size increases per request (minor overhead ~500 bytes)
- Requires clock synchronization between servers
- More complex implementation than sessions
```

---

## Alternatives Considered

Document the alternatives that were evaluated and why they were rejected.

### Alternative 1: [Name]
**Pros:**
- Advantage 1
- Advantage 2

**Cons:**
- Disadvantage 1
- Disadvantage 2

**Why rejected:** [Explanation]

### Alternative 2: [Name]
**Pros:**
- Advantage 1

**Cons:**
- Disadvantage 1
- Disadvantage 2

**Why rejected:** [Explanation]

**Example:**
```
### Alternative 1: Session-Based (Current Approach)
Pros:
- Simpler implementation
- Can revoke sessions immediately
- Less data per request

Cons:
- Requires shared session store (Redis/Memcached)
- Session store becomes bottleneck at scale
- Difficult to scale across datacenters
- Requires session replication
- Lock-in to specific infrastructure

Why rejected: Does not meet scalability requirements

### Alternative 2: OAuth 2.0 / OpenID Connect
Pros:
- Industry standard
- Supports delegation
- Third-party provider option

Cons:
- Overcomplicated for internal API
- Requires external identity provider
- Extra infrastructure overhead
- Slower implementation

Why rejected: Overkill for our use case; JWT is sufficient
```

---

## Implementation Notes

Document how this decision will be implemented.

Include:
- Technology/tools that will be used
- Code patterns to follow
- Configuration needed
- Integration points
- Timeline

**Example:**
```
Use Spring Security + jjwt library
Algorithm: RS256 (RSA signatures)
Token lifetime: 1 hour for access token
Refresh token lifetime: 7 days
Key management: Store keys in environment (K8s secrets)
Validation: Add JWT filter to SecurityConfig
Pattern: Use TokenProvider for generation/validation
Encoding: JWT stored in Authorization header (Bearer scheme)
```

---

## References

Link to relevant documentation, standards, or discussions.

- [JWT RFC 7519](https://tools.ietf.org/html/rfc7519)
- [Spring Security Documentation](https://spring.io/projects/spring-security)
- [OWASP JWT Best Practices](https://tools.ietf.org/html/rfc8725)
- Related GitHub issues/PRs
- Related Slack discussions

---

## Related ADRs

Link to ADRs that this decision is related to:
- Supersedes: [ADR-XXX]
- Superseded by: [ADR-XXX]
- Related to: [ADR-XXX], [ADR-XXX]

---

## Status Timeline

| Date | Status | Notes |
|------|--------|-------|
| 2026-04-07 | Proposed | Initial proposal by Backend Team |
| 2026-04-08 | Accepted | Approved after team review |
| 2026-04-15 | Implemented | Merged to main branch |

---

**Document Version:** 1.0  
**Last Updated:** [YYYY-MM-DD]  
**Prepared By:** [Name]

---

## ADR Numbering Convention

- Start with ADR-001 for first decision
- Do NOT renumber when ADR is deprecated/superseded
- Format: `ADR-[NUMBER]-{title-in-kebab-case}.md`
- Example: `ADR-001-jwt-authentication.md`
