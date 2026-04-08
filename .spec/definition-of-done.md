# Definition of Done (DoD) - Feature Implementation

A feature is considered **DONE** when ALL of the following criteria are met:

---

## 1. Code Quality ✔️

- [ ] Code follows [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
- [ ] No SonarQube code smells, bugs, or security issues (quality gate: A or higher)
- [ ] Cyclomatic complexity < 10 per method
- [ ] No code duplication (copy-paste) > 5 lines
- [ ] All public methods have comprehensive Javadoc (including `@param`, `@return`, `@throws`)
- [ ] Private methods documented if logic is complex
- [ ] No deprecated APIs or warnings in compilation

---

## 2. Testing ✔️

- [ ] **Unit Test Coverage ≥ 80%** (measured by JaCoCo)
  - Domain layer: ≥ 90%
  - Application layer: ≥ 85%
  - Infrastructure: ≥ 70%
  - Presentation: ≥ 75%
- [ ] All acceptance criteria have automated test cases
- [ ] Integration tests for service layer use cases
- [ ] Error scenarios covered (exceptions, validations, edge cases)
- [ ] Performance benchmarks meet targets in Constitution
- [ ] No flaky tests (all tests pass consistently - run 3x)
- [ ] Test names follow convention: `should_behavior_when_condition()`

---

## 3. Specification Compliance ✔️

- [ ] Every Functional Requirement (FR-*) implemented and tested
- [ ] Every Non-Functional Requirement (NFR-*) verified
  - Performance targets met (P50, P95, P99)
  - Security requirements implemented
  - Scalability assumptions validated
- [ ] All acceptance criteria passing
- [ ] No unspecified features added (scope locked)
- [ ] Specification referenced in code comments for traceability
- [ ] Traceability matrix updated (see `/.spec/traceability.md`)

---

## 4. Documentation ✔️

- [ ] Architecture-level Javadoc complete
- [ ] Complex algorithms documented with rationale
- [ ] API documentation (Swagger/OpenAPI) updated with:
  - All endpoints documented
  - Request/response examples
  - Error responses documented
- [ ] CHANGELOG.md updated with feature summary
- [ ] Runbook/operations guide created for operational requirements
- [ ] Database schema documentation updated (if applicable)
- [ ] README updated if new configuration/setup needed

---

## 5. Security & Performance ✔️

- [ ] OWASP security check: No critical/high severity issues
  - Input validation for all user inputs
  - SQL injection prevention (parameterized queries)
  - XSS prevention for web endpoints
  - CSRF protection where applicable
- [ ] Performance profiling: Meets P95/P99 targets from Constitution
  - Database queries optimized (no N+1 queries)
  - Proper indexes on queried columns
  - Inefficient loops identified and fixed
  - Caching strategy implemented (if applicable)
- [ ] No memory leaks detected in load tests
- [ ] Exception handling does not leak sensitive information

---

## 6. Code Review ✔️

- [ ] Minimum 2 peer review approvals obtained
- [ ] All review comments addressed and resolved
- [ ] Architecture consistency verified
- [ ] No code review anti-patterns detected:
  - Massive methods (>50 lines)
  - God classes (>200 lines)
  - Tight coupling (high dependencies)
  - Missing abstractions
- [ ] PR/MR description includes:
  - Feature summary
  - Link to specification
  - Link to test coverage report

---

## 7. Deployment Readiness ✔️

- [ ] Backward compatibility maintained (or documented breaking change)
- [ ] Database migrations tested (forward and backward)
- [ ] Configuration requirements documented in `.env.example` or README
- [ ] Feature flags implemented (if needed for gradual rollout)
- [ ] Deployment runbook reviewed and approved
- [ ] Rollback plan documented and tested
- [ ] No secrets committed to repository
- [ ] Dependency versions pinned (no floating versions)

---

## 8. Monitoring & Observability ✔️

- [ ] Appropriate logging added for troubleshooting:
  - Application startup/shutdown
  - Business-critical operations
  - Error conditions
  - Performance bottlenecks
- [ ] Metrics instrumented for monitoring:
  - Operation counters (how many times executed)
  - Performance metrics (latency, throughput)
  - Business metrics (if applicable)
- [ ] Health check endpoints updated (Kubernetes readiness/liveness)
- [ ] Alerting rules documented (if applicable)
- [ ] Log levels appropriate (INFO for normal, WARN for unusual, ERROR for failures)

---

## 9. Architectural Compliance ✔️

- [ ] Proper layer separation maintained:
  - Domain layer: No Spring dependencies
  - Application layer: No DB dependencies
  - Infrastructure layer: Implements ports/interfaces from domain
  - Presentation layer: Minimal business logic
- [ ] Dependency injection properly configured
- [ ] No circular dependencies between packages
- [ ] Design patterns applied consistently (aggregate, repository, service)
- [ ] Configuration per Constitution guidelines

---

## Feature Completion Checklist

**Feature:** [Feature Name]  
**Assigned To:** [Team Member]  
**Start Date:** [YYYY-MM-DD]  
**Completion Date:** [YYYY-MM-DD]  
**Specification Reference:** `/.spec/specs/{feature-name}.md`

### DoD Verification Summary

```
Code Quality:              [  ] ✅ / [  ] ❌
Testing (≥80%):            [  ] ✅ / [  ] ❌
Specification Compliance:  [  ] ✅ / [  ] ❌
Documentation:             [  ] ✅ / [  ] ❌
Security & Performance:    [  ] ✅ / [  ] ❌
Code Review (2 approvals): [  ] ✅ / [  ] ❌
Deployment Ready:          [  ] ✅ / [  ] ❌
Monitoring Setup:          [  ] ✅ / [  ] ❌
Architecture Verified:     [  ] ✅ / [  ] ❌

==============================================
OVERALL STATUS:            [  ] DONE / [  ] NOT DONE
```

---

## Definition of Done Review Process

**To verify DoD before merge:**

1. Assigned developer completes self-review using this checklist
2. First peer reviewer verifies code quality + security sections
3. Second peer reviewer verifies testing + compliance sections
4. Tech lead or architect verifies architectural compliance
5. All checkboxes marked ✅ before merge to main branch

---

## Notes

- **Small Bugs/Hotfixes**: Must meet sections 1-3, 5-6 minimum
- **Database-Heavy Features**: Add additional section for schema validation
- **Performance-Critical Features**: Benchmark against Constitution targets BEFORE completing
- **Compliance Features**: Include audit trail verification in section 8

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Status:** Template Ready
