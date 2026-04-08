# Traceability Matrix

**Purpose:** Ensure complete coverage between business requirements, specifications, implementation, and tests.

**Last Updated:** 2026-04-07  
**Maintained By:** [Team/Role]

---

## Overview

This document maintains traceability between:
- 📋 Business Requirements → Functional Requirements (FR)
- 🎯 Functional Requirements → Code Implementation
- ✅ Requirements → Test Coverage
- 📚 Requirements → API Documentation
- 📊 Requirements → Database Schema

---

## How to Use This Matrix

### When Adding a New Feature:

1. **Create specification** (`/.spec/specs/{feature-name}.md`)
   - List all FRs: FR-1, FR-2, FR-3, etc.
   - List all NFRs with specific targets

2. **Implementation begins**
   - Reference FR in code: `// Implements: FR-1: User Login`
   - Track file paths in this matrix

3. **Write tests**
   - Map test classes to requirements
   - Mark in "Test Class" column

4. **Update this matrix**
   - Add rows for each FR
   - Link to code locations
   - Link to test locations

### Before Merging:

- Verify all FRs have ✅ in "Code Status" column
- Verify all FRs have ✅ in "Test Status" column
- Verify all FRs have ✅ in "API Doc Status" column

---

## Template: Feature Traceability

```markdown
# Feature: [Feature Name]

**Specification:** `/.spec/specs/{feature-name}.md`  
**Plan:** `/.spec/plans/{feature-name}.plan.md`  
**Tasks:** `/.spec/tasks/{feature-name}.tasks.md`  

| # | Requirement | Type | Description | Code Location | Test Class | Test Method | API Endpoint | DB Tables | Code ✅ | Test ✅ | Doc ✅ |
|---|---|---|---|---|---|---|---|---|---|---|---|
| FR-1 | User Login | Functional | Enable user login with credentials | User.java, LoginService.java | LoginServiceTests | testSuccessfulLogin() | POST /api/auth/login | users | ✅ | ✅ | ✅ |
| FR-2 | Token Validation | Functional | Validate JWT tokens | TokenService.java, JwtTokenProvider.java | TokenServiceTests | testValidToken() | All protected | - | ✅ | ✅ | ✅ |
| NFR-1 | Performance | Non-Functional | Login ≤ 200ms P95 | JwtTokenProvider.java | PerformanceTests | testLoginResponseTime() | - | - | ✅ | ✅ | ✅ |
```

---

## Example: User Authentication Feature

# Feature: User Authentication with JWT

**Specification:** `/.spec/specs/user-authentication.md`  
**Plan:** `/.spec/plans/user-authentication.plan.md`

| Req # | Requirement | Type | Description | Code Location | Test Class | Test Method | Endpoint | Database | Code | Test | Doc |
|---|---|---|---|---|---|---|---|---|---|---|---|
| FR-1 | User Login | Functional | User submits username/password, receives JWT | `User.java` `LoginService.java` | `LoginServiceTests` | `should_returnToken_when_credentialsValid()` | `POST /api/auth/login` | `users` | ✅ | ✅ | ✅ |
| FR-2 | Token Validation | Functional | System validates JWT signature per request | `TokenService.java` `JwtTokenProvider.java` `SecurityConfig.java` | `TokenServiceTests` `AuthIntegrationTests` | `should_rejectExpiredToken()` `should_rejectInvalidSignature()` | All protected endpoints | `refresh_tokens` | ✅ | ✅ | ✅ |
| FR-3 | Role-Based Access | Functional | Endpoints protected by user roles | `SecurityConfig.java` `User.java` | `AuthorizationTests` | `should_forbid_unauthorized_role()` | All endpoints | `user_roles` | ✅ | ✅ | ✅ |
| NFR-1 | Password Security | Non-Functional | Passwords hashed with bcrypt (cost 12) | `User.java` | `UserSecurityTests` | `should_hash_password_withBcrypt12()` | - | `users` | ✅ | ✅ | ✅ |
| NFR-2 | Login Performance | Non-Functional | Login API ≤ 200ms (P95) | `JwtTokenProvider.java` | `PerformanceTests` | `should_completionLogin_within200ms()` | `POST /api/auth/login` | - | ✅ | ✅ | ✅ |
| NFR-3 | Token Validation Performance | Non-Functional | Token validation ≤ 5ms overhead | `JwtTokenProvider.java` | `PerformanceTests` | `should_validateToken_within5ms()` | All protected | - | ✅ | ✅ | ✅ |
| AC-1 | Valid Login Accepted | Acceptance | User can login with valid credentials | `LoginService.java` | `LoginIntegrationTests` | `should_acceptValidCredentials()` | `POST /api/auth/login` | `users` | ✅ | ✅ | ✅ |
| AC-2 | Invalid Login Rejected | Acceptance | Invalid credentials rejected with 401 | `LoginService.java` | `LoginIntegrationTests` | `should_reject_invalidCredentials()` | `POST /api/auth/login` | - | ✅ | ✅ | ✅ |
| AC-3 | Token Structure | Acceptance | Token contains user ID and roles | `TokenService.java` | `TokenServiceTests` | `should_includeUserIdAndRoles()` | `POST /api/auth/login` | - | ✅ | ✅ | ✅ |

### Code Locations (Detailed)

**User.java** (`src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/`)
- Implements: FR-1 (password validation), FR-3 (roles), NFR-1 (security)
- Methods: `User.isPasswordValid()`, `User.getRoles()`

**LoginService.java** (`src/main/java/br/com/rsdconsultoria/demo/core/application/services/`)
- Implements: FR-1, AC-1, AC-2
- Method: `LoginService.authenticate(username, password)`

**TokenService.java** (`src/main/java/br/com/rsdconsultoria/demo/core/application/services/`)
- Implements: FR-2, AC-3
- Methods: `TokenService.generateToken()`, `TokenService.validateToken()`

**JwtTokenProvider.java** (`src/main/java/br/com/rsdconsultoria/demo/infrastructure/services/`)
- Implements: FR-2, NFR-2, NFR-3
- Methods: `JwtTokenProvider.createAccessToken()`, `JwtTokenProvider.validateSignature()`

**SecurityConfig.java** (`src/main/java/br/com/rsdconsultoria/demo/config/`)
- Implements: FR-3, FR-2
- Configuration for JWT filter and role-based access control

### Test Coverage Summary

| Test Class | Location | Requirements Covered | Test Count | Coverage % |
|---|---|---|---|---|
| LoginServiceTests | `src/test/java/.../services/` | FR-1, AC-1, AC-2 | 5 | 92% |
| TokenServiceTests | `src/test/java/.../services/` | FR-2, AC-3 | 8 | 88% |
| AuthIntegrationTests | `src/test/java/.../controller/` | FR-2, FR-3, AC-1, AC-2, AC-3 | 12 | 95% |
| PerformanceTests | `src/test/java/.../performance/` | NFR-2, NFR-3 | 4 | 100% |
| UserSecurityTests | `src/test/java/.../domain/` | NFR-1 | 3 | 100% |
| **TOTAL** | | **All Requirements** | **32** | **94%** |

### API Documentation

| Endpoint | Requirement | OpenAPI Doc | Status |
|---|---|---|---|
| `POST /api/auth/login` | FR-1, AC-1, AC-2 | ✅ Updated | Documented |
| `POST /api/auth/refresh` | FR-2, AC-3 | ✅ Updated | Documented |
| `GET /api/user/profile` | FR-3 | ✅ Updated | Protected by role |

### Database Schema Mapping

| Table | Requirement | Columns | Indexes | Status |
|---|---|---|---|---|
| `users` | FR-1, NFR-1 | id, username, password_hash, email, roles, active | PK: id, UK: username | ✅ Created |
| `refresh_tokens` | FR-2 | id, user_id, token, expires_at, created_at | PK: id, UK: token, FK: user_id | ✅ Created |
| `user_roles` | FR-3 | id, user_id, role_name | PK: id, FK: user_id | ✅ Created |

---

## Legend

| Symbol | Meaning |
|---|---|
| ✅ | Complete / Verified / Documented |
| ⚠️ | In Progress / Partial / Needs Review |
| ❌ | Not Started / Gap Identified |
| - | Not Applicable |

---

## Traceability Gaps

Use this section to track any gaps found during verification:

### Current Gaps

- [ ] Requirement not yet implemented
- [ ] No test coverage for requirement
- [ ] Missing API documentation
- [ ] Missing database schema

### Resolved Gaps

| Date | Requirement | Gap | Resolution |
|---|---|---|---|
| 2026-04-08 | FR-2 | Missing performance test | Added PerformanceTests class |
| 2026-04-09 | NFR-1 | Password hashing not validated | Added UserSecurityTests |

---

## Verification Checklist (Before Release)

- [ ] All FRs have implementation code (✅ in Code column)
- [ ] All FRs have test coverage (✅ in Test column)
- [ ] All NFRs have verification method documented
- [ ] All acceptance criteria traced to tests
- [ ] No unspecified requirements implemented (no wild cards)
- [ ] Test coverage ≥ 80% overall
- [ ] API documentation complete
- [ ] Database schema documented
- [ ] No gaps or ⚠️ symbols remaining

---

## How to Add New Features

1. Create new section with "Feature: [Name]"
2. Reference specification document
3. List all requirements (FR-*, NFR-*, AC-*)
4. Add rows to traceability table
5. During implementation: update "Code Location" column
6. During testing: update "Test Class" and "Test Method" columns
7. Before merge: verify all ✅ columns are marked
8. Update database and API sections if applicable

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Maintained By:** [Engineering Team]
