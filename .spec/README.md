# Specification-Driven Development (SDD) Process

**Version:** 1.0  
**Last Updated:** 2026-04-07  
**Project:** %PROJECT_NAME%  
**Stack:** Spring Boot 4.0+ | Java 25 | Maven

---

## Table of Contents

1. [Overview](#overview)
2. [SDD Phases](#sdd-phases)
3. [Directory Structure](#directory-structure)
4. [Phase-by-Phase Guide](#phase-by-phase-guide)
5. [GitHub Copilot Chat Commands](#github-copilot-chat-commands)
6. [Examples](#examples)
7. [Quality Assurance & Validation](#quality-assurance--validation)
8. [Troubleshooting & Best Practices](#troubleshooting--best-practices)

---

## Overview

### What is SDD?

Specification-Driven Development (SDD) is a systematic approach to software development where specifications precede implementation. This ensures clarity, reduces rework, improves code quality, and facilitates team collaboration.

### Core Principles

- **Specification First**: Define requirements thoroughly before coding
- **Layered Architecture**: Separate concerns across presentation, application, domain, and infrastructure layers
- **Quality by Design**: Embed quality gates at each phase
- **Traceability**: Maintain clear relationships between specifications, plans, tasks, and code
- **Collaboration**: Enable GitHub Copilot Chat and team input throughout the process

### Expected Outcomes

✅ Clear, documented requirements  
✅ Architectural decisions captured  
✅ Reduced scope creep  
✅ Improved code quality  
✅ Better knowledge transfer  
✅ Easier maintenance  
✅ Faster onboarding for new team members  

---

## SDD Phases

```
Phase 1: SPECIFICATION  →  Phase 2: PLANNING  →  Phase 3: IMPLEMENTATION  →  Phase 4: VALIDATION
   (What & Why)            (How & When)           (Code & Integration)        (Testing & Review)
```

### Phase 1: Specification
**Duration:** 1-3 days per feature  
**Deliverable:** `.spec/specs/{feature-name}.md`  
**Goal:** Define business requirements, success criteria, and constraints

### Phase 2: Planning  
**Duration:** 1-2 days per feature  
**Deliverable:** `.spec/plans/{feature-name}.plan.md` + `.spec/tasks/{feature-name}.tasks.md`  
**Goal:** Define technical approach and task breakdown

### Phase 3: Implementation
**Duration:** Variable per feature scope  
**Deliverable:** Code in `src/` directory  
**Goal:** Write clean, tested code following SDD specifications

### Phase 4: Validation
**Duration:** 1-2 days per feature  
**Deliverable:** Test results, code review approval  
**Goal:** Verify specification compliance and code quality

---

## Directory Structure

```
.spec/
├── README.md                                    # This file - SDD process guide
├── constitution.md                              # Architectural & quality standards
├── definition-of-done.md                        # ✅ DoD checklist 
├── reference-architecture.md                    # 🏗️ Patterns & best practices 
├── traceability.md                              # 🔗 Requirements traceability 
├── risks-and-dependencies.md                    # ⚠️ Risk management 
├── nfr-specifications.md                        # 📊 Non-functional requirements guide 
├── VERSIONING-GUIDE.md                          # 🔄 Change management 
├── lessons-learned-template.md                  # 📖 Retrospectives 
├── acceptance-tests-template.gherkin            # ✅ Gherkin acceptance tests 
├── adr/                                         # Architecture Decision Records 
│   └── ADR-000-TEMPLATE.md                      # ADR template
├── specs/                                       # Feature specifications
│   ├── feature-name.md                          # Specification template
│   ├── user-authentication.md                   # Example: User authentication spec
│   └── leave-request-workflow.md                # Example: Leave workflow spec
├── plans/                                       # Feature plans & technical design
│   ├── feature-name.plan.md                     # Plan template
│   ├── user-authentication.plan.md              # Example plan
│   └── leave-request-workflow.plan.md           # Example plan
├── tasks/                                       # Decomposed implementation tasks
│   ├── feature-name.tasks.md                    # Tasks template
│   ├── user-authentication.tasks.md             # Example tasks breakdown
│   └── leave-request-workflow.tasks.md          # Example tasks breakdown
├── acceptance-tests/                            # Gherkin BDD tests 
│   ├── feature-name.gherkin                     # Template
│   └── user-authentication.gherkin              # Example
└── lessons-learned/                             # Post-feature retrospectives 
    └── YYYY-MM-DD-feature-name.md               # Capture format
```

### File Naming Conventions

- **Spec files**: `{feature-name}.md` (e.g., `user-authentication.md`)
- **Plan files**: `{feature-name}.plan.md`
- **Task files**: `{feature-name}.tasks.md`
- **Use kebab-case** for multi-word feature names: `leave-request-workflow.md`

---

## Phase-by-Phase Guide

### Phase 1: SPECIFICATION ✍️

#### Purpose
Capture WHAT needs to be built and WHY, without discussing HOW.

#### Deliverable File
Create: `/.spec/specs/{feature-name}.md`

#### Key Sections

```markdown
# Feature Spec: [Feature Name]

**Status:** Draft | In Review | Approved | In Development | Complete
**Version:** 1.0
**Last Updated:** [YYYY-MM-DD]
**Owner:** [Team/Person]

## Overview
- 1-2 sentence feature summary

## Business Context
- Problem Statement: What challenge does this solve?
- Stakeholders: Who benefits?
- Priority: High/Medium/Low

## Goals & Success Criteria
- Business goals
- Measurable success metrics

## Requirements
### Functional Requirements (FR)
- FR-1: [Requirement Title]
  - Description: [What the system must do]
  - Priority: [High/Medium/Low]
  - Dependencies: [Any related features]

### Non-Functional Requirements (NFR)
- NFR-1: Performance requirements
- NFR-2: Security requirements
- NFR-3: Scalability requirements

## Acceptance Criteria
- [ ] Specific, testable criteria

## Out of Scope
- What's explicitly NOT included in this feature
```

#### Checklist for Specification Approval

- [ ] Feature goal is clear and measurable
- [ ] All acceptance criteria are testable
- [ ] Non-functional requirements are specified
- [ ] Dependencies are identified
- [ ] Success metrics are defined
- [ ] Stakeholder has validated the spec
- [ ] No implementation details in functional requirements

#### GitHub Copilot Chat Commands

**To generate a feature spec:**
```
@workspace Generate a comprehensive feature specification for [feature description].
Include sections for business context, functional requirements, non-functional requirements, 
acceptance criteria, and success metrics. Use this template structure as guidance.
```

**Example:**
```
@workspace Generate a comprehensive feature specification for "User Authentication with JWT".
This feature should allow users to login with username/password, receive a JWT token,
and authenticate subsequent requests. Include security requirements, performance targets,
and error handling scenarios.
```

**To validate an existing spec:**
```
@workspace Review this specification for completeness and clarity.
Ensure all acceptance criteria are testable, dependencies are identified,
and the requirements are free of implementation details.
```

---

### Phase 2: PLANNING 🗺️

#### Part 2A: Create Feature Plan

**Purpose:** Define technical solution approach and architecture decisions

**Deliverable File:** Create: `/.spec/plans/{feature-name}.plan.md`

**Key Sections:**

```markdown
# Feature Plan: [Feature Name]

**Status:** Draft | In Progress | Complete
**Author:** [Your Name]
**Spec Reference:** /.spec/specs/{feature-name}.md
**Approval Date:** [YYYY-MM-DD]

## 1. Overview
- Summary aligned with specification
- Business value statement
- Success criteria checklist

## 2. Specifications Summary
- Reference to approved spec
- Key functional requirements
- Key non-functional requirements
- Constraints & assumptions

## 3. Technical Design

### Architecture Changes
- Which layers are affected? (Presentation, Application, Domain, Infrastructure)
- New entities, services, or components
- Integration points

### Database Design
- New tables/schema changes
- Indexes required
- Data migration strategy (if applicable)

### API Contract (if applicable)
- Endpoints
- Request/Response models
- Error responses

### External Dependencies
- Third-party services
- Configuration needed
- Security/credentials management

## 4. Implementation Phases
- Phase breakdown (if feature is large)
- Dependencies between phases
- Estimated timelines

## 5. Risks & Mitigation
- Identified risks
- Impact & likelihood assessment
- Mitigation strategies

## 6. Resources Required
- Team members & skills needed
- Infrastructure/environment needs
- Tools/libraries

## 7. Definition of Done
- Code quality criteria
- Test coverage minimum (typically 80%)
- Documentation required
- Code review requirements
```

#### GitHub Copilot Chat Commands

**To generate a technical plan:**
```
@workspace Generate a detailed technical plan for implementing the "User Authentication with JWT" feature.
Reference the specification at /.spec/specs/user-authentication.md.
Include architecture decisions, database schema changes, API contracts, and identify risks.
Consider the Spring Boot 4.0 + Java 25 stack and DDD architecture.
```

**To validate the plan against the specification:**
```
@workspace Review this plan against the specification at /.spec/specs/{feature-name}.md.
Ensure the technical approach addresses all functional requirements, meets non-functional targets,
and aligns with our Constitution guidelines in /.spec/constitution.md.
```

---

#### Part 2B: Create Task Breakdown

**Purpose:** Decompose the feature into concrete implementation tasks

**Deliverable File:** Create: `/.spec/tasks/{feature-name}.tasks.md`

**Structure:**

```markdown
# Feature: [Feature Name]

**Spec Reference:** /.spec/specs/{feature-name}.md
**Plan Reference:** /.spec/plans/{feature-name}.plan.md
**Status:** Not Started | In Progress | Under Review | Completed

## Task Breakdown

### Phase 1: Analysis & Design
- [ ] **Specification Review** - Validate spec completeness
- [ ] **Architecture Design** - Design technical solution
- [ ] **Database Schema Design** - Design data model

### Phase 2: Domain Layer
- [ ] **Domain Model Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/core/domain/
  - Acceptance: All entities created, business rules enforced

- [ ] **Domain Service Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/core/domain/services/
  - Acceptance: Domain logic encapsulated, tested

### Phase 3: Application Layer
- [ ] **Application Service Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/core/application/
  - Acceptance: Use cases orchestrated, integration tested

- [ ] **DTO Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/dto/
  - Acceptance: API contracts defined, validation added

### Phase 4: Infrastructure Layer
- [ ] **Repository Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/infrastructure/repositories/
  - Acceptance: All persistence operations implemented

- [ ] **External Service Integration**
  - Path: src/main/java/br/com/rsdconsultoria/demo/infrastructure/services/
  - Acceptance: Third-party integration complete, errors handled

### Phase 5: Presentation Layer
- [ ] **Controller Implementation**
  - Path: src/main/java/br/com/rsdconsultoria/demo/controller/
  - Acceptance: REST endpoints working, error handling added

- [ ] **Exception Handling**
  - Path: src/main/java/br/com/rsdconsultoria/demo/controller/
  - Acceptance: Global exception handler configured

### Phase 6: Testing
- [ ] **Unit Tests** - Minimum 80% code coverage
- [ ] **Integration Tests** - Key business flows tested
- [ ] **API Tests** - REST endpoints validated
- [ ] **Performance Tests** - Performance targets verified

### Phase 7: Documentation & Review
- [ ] **Code Documentation** - Javadoc added
- [ ] **API Documentation** - OpenAPI/Swagger updated
- [ ] **Runbook** - Operations documentation
- [ ] **Code Review** - At least 2 approvals

## Task Dependencies

Define which tasks must complete before others can start.

## Time Estimates

Provide realistic estimates in story points or hours for each task.
```

#### GitHub Copilot Chat Commands

**To generate task breakdown:**
```
@workspace Generate a detailed task breakdown for implementing the "User Authentication" feature.
Reference the plan at /.spec/plans/user-authentication.plan.md.
Break down tasks across our architecture layers (Domain, Application, Infrastructure, Presentation).
Include acceptance criteria for each task and identify dependencies.
Estimate complexity for each task.
```

**To create a checklist for team coordination:**
```
@workspace Create a dependency diagram and task prioritization for these tasks:
[Paste task list]
Identify critical path, blockers, and parallel work opportunities.
```

---

### Phase 3: IMPLEMENTATION 💻

#### Before You Start

1. ✅ Specification is APPROVED (status: "Approved" in spec)
2. ✅ Plan is REVIEWED (status: "In Progress" or "Complete" in plan)
3. ✅ Tasks are DECOMPOSED (all tasks listed in tasks document)
4. ✅ Team is ASSIGNED (each task has owner)

#### Layer-by-Layer Implementation Sequence

**Layer 1: Domain (Core Business Logic)**
```
src/main/java/br/com/rsdconsultoria/demo/core/domain/
├── entities/              # Domain entities
│   └── {Entity}.java
├── value-objects/         # Immutable value objects
│   └── {ValueObject}.java
├── services/              # Domain services
│   └── {Service}.java
└── exceptions/            # Domain-specific exceptions
    └── {Exception}.java
```

**Layer 2: Application (Use Cases & Orchestration)**
```
src/main/java/br/com/rsdconsultoria/demo/core/application/
├── use-cases/             # Use case implementations
│   └── {UseCase}.java
├── services/              # Application services
│   └── {Service}.java
└── dto/                   # Data transfer objects
    └── {DTO}.java
```

**Layer 3: Infrastructure (Technical Implementation)**
```
src/main/java/br/com/rsdconsultoria/demo/infrastructure/
├── repositories/          # Data access layer
│   └── {Repository}.java
├── services/              # External integrations
│   └── {Service}.java
└── config/                # Infrastructure configuration
    └── {Config}.java
```

**Layer 4: Presentation (REST API)**
```
src/main/java/br/com/rsdconsultoria/demo/controller/
├── {Feature}Controller.java
├── GlobalExceptionHandler.java  # Error handling
└── dto/                   # API request/response DTOs
    └── {DTO}.java
```

#### GitHub Copilot Chat Commands

**To generate domain entity code:**
```
@workspace Generate a domain entity class for [entity name] based on this specification:
[Paste relevant parts of the spec]

The class should:
- Follow DDD principles
- Include value validation in constructor
- Implement domain business rules
- Use immutability where appropriate
- Include comprehensive Javadoc

Target path: src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/
```

**To generate application service code:**
```
@workspace Generate an application service for [use case] that orchestrates
domain logic for this requirement:
[Paste requirement from spec]

The service should:
- Use Spring's dependency injection
- Delegate domain logic to entities/domain services
- Handle transactions
- Include proper exception handling
- Be thoroughly documented

Target path: src/main/java/br/com/rsdconsultoria/demo/core/application/services/
```

**To generate repository code:**
```
@workspace Generate a repository interface and implementation for persisting [entity].

The repository should:
- Define query methods for all required access patterns
- Include Spring Data JPA annotations
- Add custom queries where needed
- Include proper exception handling
- Support pagination for list operations

Target path: src/main/java/br/com/rsdconsultoria/demo/infrastructure/repositories/
```

**To generate REST controller code:**
```
@workspace Generate a REST controller for [feature] that exposes the following endpoints:
[List endpoints from API contract in plan]

The controller should:
- Follow REST conventions
- Map DTOs to domain entities
- Use the application service layer
- Include proper error handling
- Add OpenAPI/Swagger annotations
- Include comprehensive validation

Target path: src/main/java/br/com/rsdconsultoria/demo/controller/
```

**To implement comprehensive unit tests:**
```
@workspace Generate comprehensive unit tests for this class:
[Paste class code]

The tests should:
- Cover all public methods
- Test both happy paths and error cases
- Verify domain validations
- Mock dependencies appropriately
- Achieve minimum 80% code coverage
- Use descriptive test names

Target path: src/test/java/br/com/rsdconsultoria/demo/[corresponding layer]/
```

#### Implementation Best Practices

- ✅ **One task at a time** - Complete, test, and commit before moving to next
- ✅ **Test-driven where possible** - Write tests as specification of behavior
- ✅ **Follow the Constitution** - Adhere to code quality standards in `.spec/constitution.md`
- ✅ **Small, focused commits** - Each commit should map to one task
- ✅ **Reference specifications** - Add comments linking code to spec requirements
- ✅ **Document as you go** - Add Javadoc and comments for complex logic

---

### Phase 4: VALIDATION ✓

#### Quality Gates

Before merging any code, verify:

1. **Specification Compliance**
   ```
   All functional requirements from spec are implemented
   All acceptance criteria from spec are verified
   No scope creep or unspecified features added
   ```

2. **Code Quality**
   ```
   Code follows Constitution guidelines
   Code style is consistent (Google Java Style Guide)
   Security best practices applied
   Performance targets met (per Constitution)
   ```

3. **Test Coverage**
   ```
   Minimum 80% code coverage achieved
   All acceptance criteria have automated tests
   Integration tests for business flows
   Performance tests for critical operations
   ```

4. **Documentation**
   ```
   Javadoc complete for public methods
   API documentation updated (Swagger/OpenAPI)
   README or CHANGELOG updated if needed
   Complex logic has explanatory comments
   ```

5. **Code Review**
   ```
   Minimum 2 peer reviews approved
   All review comments addressed
   No critical issues or security vulnerabilities
   Architecture consistency verified
   ```

#### GitHub Copilot Chat Commands

**To validate specification compliance:**
```
@workspace Review this implementation against the specification at /.spec/specs/{feature-name}.md.
Verify that:
1. All functional requirements are implemented
2. All acceptance criteria are satisfied
3. Non-functional requirements are met
4. No unspecified features are present

Implementation path: src/main/java/br/com/rsdconsultoria/demo/
```

**To verify code quality:**
```
@workspace Analyze this code for compliance with our Constitution (.spec/constitution.md):
[Paste code or file path]

Check:
1. Architecture layer separation
2. Code style consistency
3. Error handling completeness
4. Performance alignment with targets
5. Security best practices
```

**To check test coverage:**
```
@workspace Analyze the test coverage for [feature implementation].
Review test/main tree at: src/test/java/br/com/rsdconsultoria/demo/

Identify:
1. Uncovered code paths
2. Missing test scenarios
3. Tests that could be improved
4. Integration test gaps

Suggest improvements to reach 80%+ coverage.
```

**To prepare for code review:**
```
@workspace Generate a comprehensive code review checklist for this implementation:
[Paste implementation path or code]

Include checks for:
- Specification alignment
- Code quality standards
- Architecture consistency
- Test coverage
- Performance compliance
- Security best practices
- Documentation completeness
```

---

## GitHub Copilot Chat Commands

### Command Structure & Best Practices

All commands follow this pattern:
```
@workspace [ACTION] for [FEATURE/COMPONENT]
[CONTEXT]: [Specific details, paste code/requirements]
[CONSTRAINTS]: [Technical requirements, guidelines]
[DELIVERABLE]: [What you expect as output]
```

### Repository & Workspace Context

Before issuing commands, ensure Copilot can access:
- **Specification**: `/.spec/specs/{feature-name}.md`
- **Plan**: `/.spec/plans/{feature-name}.plan.md`
- **Constitution**: `/.spec/constitution.md`
- **Existing code**: src/ directory

### Example Conversation Flow

#### Step 1: Generate Comprehensive Specification

```
@workspace Generate a detailed feature specification for "Leave Request Workflow".

Context:
- This is a Spring Boot 4.0 REST API
- We follow DDD architecture (Domain, Application, Infrastructure, Presentation layers)
- Target audience: HR managers and employees

Requirements to address:
1. Employees submit leave requests with dates, type, and reason
2. Department managers review and approve/reject
3. HR admin can override decisions
4. Email notifications on status changes
5. Audit trail for all changes

Include sections for:
- Business context and stakeholders
- Functional requirements (FR-1, FR-2, etc.)
- Non-functional requirements (performance, security, scalability)
- Acceptance criteria (testable conditions)
- Success metrics

Use the template from /.spec/specs/feature-name.md
Target path: /.spec/specs/leave-request-workflow.md
```

#### Step 2: Generate Technical Plan

```
@workspace Create a comprehensive technical plan for "Leave Request Workflow".

Reference specification: /.spec/specs/leave-request-workflow.md

Design considerations:
- We use Spring Boot 4.0 with Java 25
- Database: SQL with Spring Data JPA
- Performance targets from Constitution: P50 ≤ 100ms, P95 ≤ 300ms
- Target 1000 req/sec throughput

Your plan should include:
1. Architecture changes (entity/service/controller additions)
2. Database schema (new tables, indexes)
3. REST API contract (endpoints, request/response DTOs)
4. External integrations (email service, etc.)
5. Risk assessment and mitigation
6. Implementation phases and dependencies

Target path: /.spec/plans/leave-request-workflow.plan.md
```

#### Step 3: Generate Task Breakdown

```
@workspace Decompose this feature into implementation tasks.

Plan reference: /.spec/plans/leave-request-workflow.plan.md
Specification: /.spec/specs/leave-request-workflow.md

Our architecture layers:
- Domain: src/main/java/br/com/rsdconsultoria/demo/core/domain/
- Application: src/main/java/br/com/rsdconsultoria/demo/core/application/
- Infrastructure: src/main/java/br/com/rsdconsultoria/demo/infrastructure/
- Presentation: src/main/java/br/com/rsdconsultoria/demo/controller/

Tasks should:
- Be ordered by dependencies
- Include acceptance criteria
- Specify file paths
- Estimate complexity (S/M/L/XL)
- Group into logical phases

Target path: /.spec/tasks/leave-request-workflow.tasks.md
```

#### Step 4: Generate Domain Layer Implementation

```
@workspace Generate the domain layer implementation for LeaveRequest entity.

Specification excerpt: [Paste relevant requirements]
Plan excerpt: [Paste database design]

The entity should:
- Implement DDD principles with value objects where appropriate
- Validate business rules in constructor
- Enforce immutability for value objects
- Include domain logic methods (e.g., approve(), reject())
- Use enums for status and leave type
- Include comprehensive Javadoc

Generate:
1. LeaveRequest.java (aggregate root)
2. LeaveType.java (enum)
3. LeaveStatus.java (enum)
4. LeaveRequestException.java (domain exception)

Target path: src/main/java/br/com/rsdconsultoria/demo/core/domain/
```

#### Step 5: Generate Application Service

```
@workspace Generate the application service for leave request approval workflow.

Domain entities: [Reference to domain layer implementation]
Specification: [Reference to spec requirements for approval flow]

Service should:
- Orchestrate domain entities and services
- Handle use case: "Manager approves or rejects leave request"
- Manage transactions
- Implement error handling
- Include email notification triggering
- Be thoroughly documented with Javadoc

Generate:
1. LeaveRequestApprovalService.java (main service)
2. LeaveRequestApprovalRequest.java (input DTO)
3. LeaveRequestApprovalResponse.java (output DTO)

Target path: src/main/java/br/com/rsdconsultoria/demo/core/application/services/
```

#### Step 6: Generate REST Controller

```
@workspace Generate REST controllers for the leave request feature.

API contract from plan:
- POST /api/leave-requests (submit new request)
- GET /api/leave-requests/{id} (get request details)
- PUT /api/leave-requests/{id}/approve (approve request)
- PUT /api/leave-requests/{id}/reject (reject request)
- GET /api/leave-requests (list requests with filtering)

Controllers should:
- Follow REST conventions
- Include input validation
- Use application services for business logic
- Add proper error handling
- Include OpenAPI/Swagger annotations
- Use appropriate HTTP status codes

Target path: src/main/java/br/com/rsdconsultoria/demo/controller/
```

#### Step 7: Generate Comprehensive Test Suite

```
@workspace Generate comprehensive unit tests for the leave request feature.

Implementation paths:
- Domain: src/main/java/br/com/rsdconsultoria/demo/core/domain/
- Application: src/main/java/br/com/rsdconsultoria/demo/core/application/services/
- Controller: src/main/java/br/com/rsdconsultoria/demo/controller/

Tests should:
- Cover all public methods
- Test both happy path and error scenarios
- Verify business rule validations
- Mock external dependencies
- Use descriptive names (should_behavior_when_condition)
- Achieve minimum 80% code coverage
- Follow AAA pattern (Arrange, Act, Assert)

Target path: src/test/java/br/com/rsdconsultoria/demo/[corresponding layers]/
```

---

## Examples

### Example 1: Complete Feature Walkthrough - "User Authentication with JWT"

#### Specification Creation

**File:** `/.spec/specs/user-authentication.md`

```markdown
# Feature Spec: User Authentication with JWT

**Status:** Approved
**Version:** 1.0
**Last Updated:** 2026-04-07
**Owner:** Backend Team

## Overview
Support secure user authentication using JWT tokens, enabling stateless API access with role-based authorization.

## Business Context
- **Problem Statement:** Enable users to securely authenticate and access the API
- **Stakeholders:** All API consumers (web, mobile)
- **Priority:** High

## Goals & Success Criteria

### Goals
- [ ] Secure authentication mechanism in place
- [ ] Stateless API design (no server sessions)
- [ ] Role-based authorization working

### Success Metrics
- Authentication latency: ≤ 100ms (P50)
- Token refresh: ≤ 50ms
- 99.9% authentication service uptime

## Requirements

### FR-1: User Login
- User submits username/password
- System validates credentials
- System returns JWT token (access token + refresh token)
- Token valid for 1 hour, refresh valid for 7 days

### FR-2: Token Validation
- System validates JWT signature for each request
- Invalid/expired tokens rejected with 401
- Refresh token enables access token renewal without login

### FR-3: Role-Based Access
- Tokens include user roles
- Endpoints protected based on roles
- Unauthorized access returns 403

### NFR-1: Security
- Passwords hashed with bcrypt (minimum cost 12)
- JWT signed with RS256 algorithm
- HTTPS only (enforced by infrastructure)
- Rate limiting: max 5 login attempts per minute per IP

### NFR-2: Performance
- Login API: ≤ 200ms P95
- Token validation: ≤ 5ms overhead per request

## Acceptance Criteria
- [ ] User can login with valid credentials
- [ ] Invalid credentials rejected
- [ ] Valid JWT token returned
- [ ] Token contains user ID and roles
- [ ] Expired token rejected
- [ ] Refresh token works correctly
- [ ] Role-based access control enforced
- [ ] All operations meet performance targets
```

#### Plan Creation

**File:** `/.spec/plans/user-authentication.plan.md`

```markdown
# Feature Plan: User Authentication with JWT

**Status:** In Progress
**Spec Reference:** /.spec/specs/user-authentication.md
**Author:** Backend Team

## 3. Design & Architecture

### System Design
Implement JWT authentication with two-token strategy (access + refresh).

Architecture changes:
- **Domain:** User entity with password validation
- **Application:** LoginService, TokenService for use cases
- **Infrastructure:** JwtTokenProvider, password encoder configuration
- **Presentation:** AuthController for login/refresh endpoints

### Database Design
```sql
-- Enhanced users table
ALTER TABLE users ADD COLUMN password_hash VARCHAR(255) NOT NULL;
ALTER TABLE users ADD COLUMN salt VARCHAR(255) NOT NULL;
ALTER TABLE users ADD COLUMN roles VARCHAR(255) NOT NULL;

-- Refresh token table
CREATE TABLE refresh_tokens (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  user_id BIGINT NOT NULL,
  token VARCHAR(512) NOT NULL UNIQUE,
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### API Contract
```
POST /api/auth/login
Request:
{
  "username": "string",
  "password": "string"
}

Response (200):
{
  "accessToken": "eyJhbGc...",
  "refreshToken": "eyJhbGc...",
  "expiresIn": 3600,
  "tokenType": "Bearer"
}

Response (401):
{
  "error": "Invalid credentials",
  "timestamp": "2026-04-07T10:00:00Z"
}

POST /api/auth/refresh
Request:
{
  "refreshToken": "eyJhbGc..."
}

Response (200):
{
  "accessToken": "eyJhbGc...",
  "expiresIn": 3600,
  "tokenType": "Bearer"
}
```

## 5. Resources & Dependencies
- **Libraries:** Spring Security, jjwt, bcrypt
- **Infrastructure:** JWT signing keys (RSA key pair for RS256)
```

#### Task Breakdown

**File:** `/.spec/tasks/user-authentication.tasks.md`

```markdown
# Feature: User Authentication with JWT

**Status:** In Progress

## Task Breakdown

### Phase 1: Design & Setup
- [ ] **Security Configuration**
  - Task: Configure Spring Security, set up JWT provider
  - Acceptance: SecurityConfig with JWT filter working
  - Estimate: M

### Phase 2: Domain Layer
- [ ] **User Entity Enhancement**
  - Task: Add password validation logic to User entity
  - Acceptance: Password validation works, cannot access plain password
  - Estimate: S
  - Files: src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/User.java

- [ ] **RefreshToken Value Object**
  - Task: Model refresh tokens in domain
  - Acceptance: RefreshToken created, expiration checked
  - Estimate: S
  - Files: src/main/java/br/com/rsdconsultoria/demo/core/domain/value-objects/RefreshToken.java

### Phase 3: Application Layer
- [ ] **LoginService**
  - Task: Implement login use case
  - Acceptance: Valid credentials accepted, invalid rejected
  - Estimate: M
  - Files: src/main/java/br/com/rsdconsultoria/demo/core/application/services/LoginService.java

- [ ] **TokenService**
  - Task: Implement token generation and validation
  - Acceptance: Tokens issued, expire, can be refreshed
  - Estimate: M
  - Files: src/main/java/br/com/rsdconsultoria/demo/core/application/services/TokenService.java

### Phase 4: Infrastructure Layer
- [ ] **JwtTokenProvider**
  - Task: Implement JWT token creation/validation
  - Acceptance: Tokens signed with RS256, validated correctly
  - Estimate: M
  - Files: src/main/java/br/com/rsdconsultoria/demo/infrastructure/services/JwtTokenProvider.java

- [ ] **RefreshTokenRepository**
  - Task: Persist refresh tokens
  - Acceptance: Can save, retrieve, and delete tokens
  - Estimate: S
  - Files: src/main/java/br/com/rsdconsultoria/demo/infrastructure/repositories/RefreshTokenRepository.java

### Phase 5: Presentation Layer
- [ ] **AuthController**
  - Task: Expose login and refresh endpoints
  - Acceptance: Endpoints return proper JWT structure
  - Estimate: S
  - Files: src/main/java/br/com/rsdconsultoria/demo/controller/AuthController.java

- [ ] **JWT Filter & Error Handling**
  - Task: Validate tokens in filter, handle auth errors
  - Acceptance: Protected endpoints require valid token
  - Estimate: M
  - Files: src/main/java/br/com/rsdconsultoria/demo/config/SecurityConfig.java

### Phase 6: Testing & Validation
- [ ] **Unit Tests**
  - Target: 80%+ coverage for domain, application, infrastructure
  - Estimate: M
  - Path: src/test/java/br/com/rsdconsultoria/demo/

- [ ] **Integration Tests**
  - Test: Full login flow, token validation, refresh flow
  - Estimate: M

- [ ] **Performance Tests**
  - Verify: Login ≤ 200ms P95, token validation ≤ 5ms
  - Estimate: S
```

#### Implementation Example: Domain Entity

```java
// src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/User.java
package br.com.rsdconsultoria.demo.core.domain.entities;

import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import java.util.Set;

/**
 * User domain entity.
 * Encapsulates user identity and authentication details.
 * 
 * Specification: /.spec/specs/user-authentication.md (FR-1, FR-3)
 */
public class User {
    private Long id;
    private String username;
    private String passwordHash;
    private String email;
    private Set<String> roles;
    private boolean active;
    
    private static final BCryptPasswordEncoder encoder = new BCryptPasswordEncoder(12);
    
    /**
     * Create new user with credentials.
     * Passwords are automatically bcrypted.
     */
    public User(String username, String plainPassword, String email, Set<String> roles) {
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("Username cannot be blank");
        }
        if (plainPassword == null || plainPassword.length() < 8) {
            throw new IllegalArgumentException("Password must be at least 8 characters");
        }
        
        this.username = username;
        this.passwordHash = encoder.encode(plainPassword);
        this.email = email;
        this.roles = roles;
        this.active = true;
    }
    
    /**
     * Validate provided password against stored hash.
     * Addresses FR-1: validate credentials during login
     */
    public boolean isPasswordValid(String plainPassword) {
        return encoder.matches(plainPassword, this.passwordHash);
    }
    
    /**
     * True if user can access the system.
     */
    public boolean isActive() {
        return active;
    }
    
    // Getters (no setter for passwordHash - immutable)
    public Long getId() { return id; }
    public String getUsername() { return username; }
    public String getEmail() { return email; }
    public Set<String> getRoles() { return Set.copyOf(roles); } // FR-3: Role-based access
}
```

#### Validation Check

**Specification Compliance Verification:**

```
✅ FR-1 (User Login): LoginService validates credentials, returns JWT
✅ FR-2 (Token Validation): AuthInterceptor validates JWT for each request
✅ FR-3 (Role-Based Access): User.getRoles() returns roles included in token
✅ NFR-1 (Security): Passwords bcrypted with cost 12, JWT signed with RS256
✅ NFR-2 (Performance): Login measured at 95ms P95, token validation <1ms
✅ All acceptance criteria implemented and tested
```

---

### Example 2: Using GitHub Copilot Chat - Real Scenario

**Scenario:** You're starting a new feature and want Copilot to help with specification

**Step 1: Start a conversation**
```
@workspace I need to create a specification for "Leave Request Workflow".

Context:
- Employees submit requests for time off
- Managers review and approve/reject
- HR can override decisions
- Email notifications on status changes
- Audit trail required

Let's start with a comprehensive feature spec using the template from /.spec/specs/feature-name.md
```

**Copilot generates spec → You review → Approve**

**Step 2: Create the plan**
```
@workspace Now generate a technical plan that implements the specification.

Key considerations:
- We use Spring Boot 4.0 + Java 25
- DDD architecture with Domain/Application/Infrastructure/Presentation layers
- Must meet performance targets: P50 ≤ 100ms
- Support 1000 req/sec throughput (from Constitution)

Include:
1. Database schema changes
2. REST API contract
3. Architecture decisions
4. Risk assessment
```

**Step 3: Break down tasks**
```
@workspace Create a task breakdown for this plan.

Structure tasks by architectural layer:
- Domain: entities, value objects, services
- Application: use cases, services
- Infrastructure: repositories, external services
- Presentation: controllers, exception handling

Include:
- Task dependencies
- Acceptance criteria
- Estimated complexity (S/M/L/XL)
- File paths where code goes
```

**Step 4: Generate implementation code**
```
@workspace Generate the domain layer for the Leave entity.

Based on spec: /.spec/specs/leave-request-workflow.md
Requirements: FR-1, FR-2, FR-3

The entity should:
- Use DDD principles
- Validate business rules
- Include status tracking
- Support approval workflow
- Have comprehensive Javadoc

Generate:
1. LeaveRequest.java (aggregate root)
2. LeaveStatus.java (enum)
3. LeaveType.java (enum)

Target: src/main/java/br/com/rsdconsultoria/demo/core/domain/entities/
```

**Step 5: Validate compliance**
```
@workspace Verify that the implementation complies with the specification.

Spec: /.spec/specs/leave-request-workflow.md
Implementation path: src/main/java/br/com/rsdconsultoria/demo/

Check:
1. All functional requirements (FR-1 through FR-5) implemented
2. All acceptance criteria testable and covered
3. Non-functional targets met
4. No unspecified features added

Generate a compliance report with:
- ✅ Met requirements
- ⚠️ Partially met
- ❌ Missing requirements
```

---

## Quality Assurance & Validation

### Pre-Implementation Checklist

Before coding on any feature:

```markdown
## Feature: [Feature Name]

### Specification Review
- [ ] Spec document exists and is completed
- [ ] All acceptance criteria are testable and specific
- [ ] No implementation details in requirements
- [ ] Business stakeholder approved the spec
- [ ] Dependencies identified and documented
- [ ] Success metrics defined and achievable

### Planning Review  
- [ ] Technical plan created
- [ ] Architecture decisions documented
- [ ] Database schema (if applicable) designed
- [ ] API contract defined (if applicable)
- [ ] Risks identified and mitigation planned
- [ ] Team consensus on approach
- [ ] Resource availability confirmed

### Task Breakdown
- [ ] All tasks identified and sequenced
- [ ] Dependencies mapped
- [ ] Acceptance criteria for each task
- [ ] Effort estimates provided
- [ ] Owner assigned to each task
- [ ] Definition of Done documented
```

### Post-Implementation Validation

After implementation, verify:

```markdown
## Implementation Validation: [Feature Name]

### Code Quality
- [ ] Code follows Constitution guidelines
- [ ] Google Java Style Guide compliance
- [ ] No security vulnerabilities (OWASP)
- [ ] Performance targets met
- [ ] No code duplication

### Testing
- [ ] Unit test coverage ≥ 80%
- [ ] Integration tests for business flows
- [ ] All acceptance criteria have tests
- [ ] Error scenarios covered
- [ ] Performance tests confirm targets

### Specification Compliance
- [ ] Every FR implemented
- [ ] Every NFR verified
- [ ] All acceptance criteria passing
- [ ] No unspecified features
- [ ] Scope not exceeded

### Documentation
- [ ] Javadoc complete
- [ ] API documentation updated
- [ Professional SDD Enhancement Templates
```
---

### ✅ 1. Definition of Done (DoD)

**Status:** ✅ **CREATED** - See [/.spec/definition-of-done.md](definition-of-done.md)

**Purpose:** Explicit quality gate checklist for feature completion

**Sections:**
- Code Quality standards
- Testing requirements (≥80% coverage)
- Specification compliance verification
- Documentation completeness
- Security & Performance validation
- Code review approval process
- Deployment readiness
- Monitoring & observability setup
- Architectural compliance
Purpose:** Link requirements → code → tests → documentation

**Sections:**
- Feature overview with specification references
- Traceability table (FR, code locations, tests, endpoints, database)
- Code location details
- Test coverage summary
- API documentation mapping
- Database schema mapping
- Verification checklist

**Template Example:**

| Req # | Requirement | Code Location | Test Class | Endpoint |
|---|---|---|---|---|
| FR-1 | User Login | User.java, LoginService.java | LoginServiceTests | POST /api/auth/login |
| FR-2 | Token Validation | TokenService.java | TokenServiceTests | All protected endpoints |
| NFR-1 | Performance | JwtTokenProvider.java | PerformanceTests | - |

**Benefits:** 
- ✅ Ensures 100% requirements coverage
- ✅ Enables compliance audits (financial, healthcare, etc.)
- ✅ Supports legal reviews of features
- ✅ 🏗️ 3. Reference Architecture & Design Patterns

**Status:** ✅ **CREATED** - See [/.spec/reference-architecture.md](reference-architecture.md)

**Purpose:** Document reusable architectural patterns and design decisions

**Sections:**
- Layered Architecture Pattern (Domain/Application/Infrastructure/Presentation layers)
- Domain-Driven Design Patterns (Aggregate, Value Object, Domain Service)
- Error Handling patterns
- Service Layer patterns
- Repository Pattern
- DTO & Mapping patterns
- Configuration Management
- Testing Patterns (unit, integration, performance)
- Performance Optimization patterns
- Anti-Patterns to avoid

**Key Patterns Documented:**
- ✅ Aggregate pattern with domain entities
- ✅ Value Object immutability
- ✅ Domain Service for multi-aggregate logic
- ✅ Repository abstraction
- ✅ Global exception handling

**Benefits:**
- ✅ Consistency across all features
- ✅ Faster development (copy proven patterns)
- ✅ Enables architecture reviews
- ✅ Facilitates refactoring
- ✅ Onboarding guide for new team members

**When to Use:** During design phase (reference patterns) and code review (verify compliance)
 📋 4. Architecture Decision Records (ADRs)

**Status:** ✅ **CREATED** - See [/.spec/adr/ADR-000-TEMPLATE.md](adr/ADR-000-TEMPLATE.md)

**Purpose:** Document significant architectural decisions with rationale for future maintainers

**Sections per ADR:**
- Context (why this decision?)
- Decision (what was chosen?)
- Rationale (why this option?)
- Consequences (benefits and trade-offs)
- Alternatives Considered (options rejected and why)
- Implementation Notes (how to build it)
- References (standards, documentation)
- Related ADRs

**Example ADR Structure:**

```markdown
ADR-001: Use JWT for Stateless Authentication

Decision: Implement JWT instead of session-based auth
Rationale:
- Stateless design enables horizontal scaling
- No shared session store bottleneck
- Standard industry pattern

Trade-offs:
+ Scales horizontally
+ Works with mobile/SPA
- Cannot revoke immediately
- Token size per request
```

**How to Create New ADRs:**
1.  📊 5. Non-Functional Requirements Specifications

**Status:** ✅ **CREATED** - See [/.spec/nfr-specifications.md](nfr-specifications.md)

**Purpose:** Define specific, measurable, testable non-functional requirements

**Sections:**
- Performance Requirements (latency SLAs by operation, throughput targets)
- Resource Efficiency (memory, CPU, disk, bandwidth)
- Security Requirements (authentication, authorization, data protection)
- Reliability Requirements (availability SLA, MTBF, MTTR, fault tolerance)
- Scalability Requirements (horizontal scaling, database scaling, connection pooling)
- Compliance & Audit Requirements (audit trails, retention, data residency)
- Testing Strategy (performance tests, security tests, reliability tests)
- Verification Checklist (before release)

**Example Metrics:**

| Operation | P50 | P95 | P99 |
|---|---|---|---|
| Login | 50ms | 100ms | 200ms |
| Token validation | 1ms | 5ms | 10ms |
| Protected endpoint | 30ms | 100ms | 300ms |

**Benefits:**
- ✅ Objective quality metrics (not "it should be fast")
- ✅ Measurable success criteria for release
- ✅ Enables performance monitoring & alerting
- ✅ Supports compliance audits

**When to Use:** During planning phase (document targets) and validation phase (verify targets met)

### Response Time SLAs
| Operation | P50 | P95 | P99 | Measurement |
|-----------|-----|-----|-----|-------------|
| Login | 50ms | 100ms | 200ms | Full request cycle |
| Query | 30ms | 80ms | 150ms | Database + serialization |
| Mutation | 40ms | 120ms | 300ms | Database + validation |

### Throughput Targets
- Sustained: 1,000 requests/second per instance
- Burst: 5,000 requests/second (30 seconds)
- Connection pool: 20 connections per instance

### Resource Efficiency
- Heap memory: ≤ 512MB baseline + feature data
- CPU: Target 60-70% under normal load
- Disk: Log rotation ≤ 100GB per instance

## Security Requirements

### Authentication
- Minimum 8-character passwords
- Password hashing: bcrypt cost 12 minimum
- Failed attempts: Rate limiting (5 per minute per IP)

### Authorization  
- Role-based access control (RBAC)
- Least privilege principle
- Audit trail for privileged operations

### Data Protection
- T ⚠️ 6. Risk & Dependency Management

**Status:** ✅ **CREATED** - See [/.spec/risks-and-dependencies.md](risks-and-dependencies.md)

**Purpose:** Proactively identify and track risks and dependencies impacting delivery

**Sections:**
- Internal/External/Infrastructure Dependencies
- Risk register (High/Medium/Low priority risks)
- Critical Path analysis (tasks determining minimum duration)
- Dependency Timeline (when each dependency needed)
- Escalation Path (if dependency blocked)
- Mitigation Strategies (action plans for high risks)
- Weekly Risk Review process

**Example Risk Tracking:**

| Risk | Impact | Probability | Mitigation |
|---|---|---|---|
| Database performance | Response times exceed SLA | Medium | Pre-production load test Week 2 |
| Email service delay | Blocks notification testing | High | Mock email service for Phase 1 |
| Scope creep | Schedule delay 1 week | High | Strict freeze after approval |

**Critical Path Example:**

```
Database Schema (3 days) CRITICAL
    ↓
Domain Model (5 days) CRITICAL
    ↓
Application Services (4 days) CRITICAL
    ↓
Integration Tests (3 days) CRITICAL

Non-critical (parallel):
Email Integration (2 days) → Can slip 2 days
Performance Testing (1 day) → Can slip 1 day

Total minimum: 17 days
```

**Benefits:**
- ✅ Early warning of delivery blockers
- ✅ Enables contingency planning
- ✅ Clear communication to stakeholders
- ✅ Prevents last-minute surprises

**When to Use:** During planning phase (identify dependencies) and weekly during development (monitor status)

|||||
|-------------|--------|-------------|-----------|
| Email service unavailable | Feature blocked | Low | Implement retry logic, fallback to in-app notifications |
| Database performance | Response times exceed SLA | Medium | Pre-production load testing, indexing strategy |
| Scope creep | Schedule delay | High | Strict requirement freeze, change control process |



## Critical Path Items

1. **Database schema** (3 days) → CRITICAL
   - Blocks: Repository implementation
   - Depends: Infrastructure team approval

2. **Email integration** (2 days)
   - Blocks: End-to-end testing
   - Depends: External team

3. **Performance testing** (1 day)
   - Blocks: Production readiness
   - Depends: Full implementation completion

**Benefits:** Proactive risk management, identifies blockers early, enables contingency planning.

---
 🔄 7. Change Management & Versioning

**Status:** ✅ **CREATED** - See [/.spec/VERSIONING-GUIDE.md](VERSIONING-GUIDE.md)

**Purpose:** Manage specification changes and maintain clear version history

**Key Sections:**
- Versioning template (for every spec)
- Change log format
- Change Request process (submit → assess → decide → implement)
- Version numbering convention (X.Y)
- Status definitions (Draft → Review → Approved → Development → Complete)
- Impact Analysis checklist
- Version communication template

**Example Change Flow:**

```
Day 8 - Change Request Submitted
    ↓
Day 9-10 - Assessment (impact analysis)
    ↓
Day 10 - Decision (approve/reject/defer)
    ↓
Day 11+ - Implementation (if approved)
    ↓
Update spec version 1.0 → 1.1
Add to Change Log
Notify team
Continue development
```

**Version Format:**

```
Version 1.0 - Initial specification
Version 1.1 - Added hourly deduction requirement
Version 1.2 - Clarified manager escalation chain
Version 2.0 - Major: Added batch approval feature
```

**Benefits:**
- ✅ Clear history of what changed and why
- ✅ Enables impact analysis for changes
- ✅ Communication trail for stakeholders
- ✅ Version control: never overwrite specs

**When to Use:** When requirements change mid-development or need clarification during implementation
**Benefits:** API contract is specification, enables client/server decoupling, auto-generates documentation.

---

### ✅ 8. Acceptance Test Specifications (BDD)

**Status:** ✅ **CREATED** - See [/.spec/acceptance-tests-template.gherkin](acceptance-tests-template.gherkin)

**Purpose:** Document acceptance criteria in executable Gherkin format (BDD)

**Format:** 
- One `.gherkin` file per feature
- Follows Gherkin syntax (Given/When/Then)
- Maps to acceptance criteria (AC-1, AC-2, etc.)
- Compatible with Cucumber/JBehave frameworks

**Example Scenario:**

```gherkin
Feature: User Authentication with JWT
  As an API consumer
  I want to authenticate with username/password
  So that I can access protected resources

  Scenario: AC-1 - Successful login with valid credentials
    Given a user with username "testuser" and password "SecurePass123"
    When I submit a login request with correct credentials
    Then I should receive HTTP 200 OK
    And the response should contain a valid JWT token
    And the token should be valid for 1 hour
    And the token should contain my user ID

  Scenario: AC-2 - Failed login with invalid credentials
    Given a user with username "testuser"
    When I submit a login request with password "WrongPassword"
    Then I should receive HTTP 401 Unauthorized
    And no JWT token should be returned
```

**File Structure:**
```
/.spec/acceptance-tests/
├── feature-name.gherkin          # Template
├── user-authentication.gherkin    # Example
└── leave-request-workflow.gherkin # Example
```

 📖 9. Lessons Learned & Retrospectives

**Status:** ✅ **CREATED** - See [/.spec/lessons-learned-template.md](lessons-learned-template.md)

**Purpose:** Capture insights and improvements after feature completion

**File Structure:**
```
/.spec/lessons-learned/
├── YYYY-MM-DD-feature-name.md   # One per completed feature
└── 2026-04-21-user-authentication.md  # Example
```

**Sections in Template:**
- Overview (what was built)
- What Went Well ✅ (successes, why, recommendations)
- What Could Be Better 🔄 (problems, root causes, actions)
- Reusable Components 🔧 (capture for next features)
- Metrics & Data 📊 (schedule, quality, team satisfaction)
- Recommendations (priority 1, 2, 3)
- Knowledge Transfer 📚 (what team learned)
- Issues & Risk Analysis ⚠️ (what went wrong)
- Team Feedback 💬 (what team would do differently)
- Follow-Up Actions (who, what, when)

**Example Metrics Captured:**

| Metric | Target | Actual | Variance |
|---|---|---|---|
| Schedule | 14 days | 15 days | +1 day |
| Code coverage | ≥ 80% | 85% | ✅ |
| P95 performance | ≤ 100ms | 95ms | ✅ |
| Team satisfaction | 7/10 | 9/10 | ✅ |

**How to Run Retrospectives:**

1. **After completion:** Team writes lessons learned
2. **Team review meeting:** Discuss, identify patterns
3. **Prioritize:** What to implement immediately vs. later
4. **Archive:** Save in `/.spec/lessons-learned/` for future reference
5. **Action items:** Track in follow-up section

**Benefits:**
- ✅ Organizational learning (captured tribal knowledge)
- ✅ Process improvement (identify patterns)
- ✅ Team morale (recognize successes)
- ✅ Prevents repeating mistakes
- ✅ Celebrates reusable components

**When to Use:** After each feature completes (weekly if multiple features finishing)

## Testing Patterns

see testing guide...

**Benefits:** Consistency across features, faster development, enables code reviews, facilitates refactoring.

---

#### 9. **Definition of Done (DoD)** ✔️

**Recommendation: Create `/.spec/definition-of-done.md`**

Explicit quality gate checklist:

```markdown
# Definition of Done - Feature Implementation

A feature is considered DONE when:

## Code Quality
- [ ] Code follows Google Java Style Guide
- [ ] No SonarQube code smells, bugs, or security issues
- [ ] Cyclomatic complexity < 10 per method
- [ ] No code duplication (copy-paste) > 5 lines
- [ ] All public methods have Javadoc (including @param, @return, @throws)

## Testing
- [ ] Unit test coverage ≥ 80% (measured by JaCoCo)
- [ ] All acceptance criteria have automated test cases
- [ ] Integration tests for service layer use cases
- [ ] Performance benchmarks meet targets in Constitution
- [ ] No flaky tests (all tests pass consistently)

## Specification Compliance
- [ ] Every functional requirement (FR-*) implementedand tested
- [ ] Every non-functional requirement (NFR-*) verified
- [ ] All acceptance criteria passing
- [ ] No unspecified features added (scope locked)

## Documentation
- [ ] Architecture-level Javadoc complete
- [ ] Complex algorithms documented
- [ ] API documentation (Swagger/OpenAPI) updated
- [ ] CHANGELOG.md updated with feature summary
- [ ] Runbook/operations guide created for operational requirements

## Security & Performance
- [ ] OWASP security check: No critical/high severity issues
- [ ] Performance profiling: Meets P95/P99 targets
- [ ] SQL queries: Proper indexes, no N+1 queries
- [ ] No memory leaks detected in load tests

## Code Review
- [ ] Minimum 2 peer review approvals
- [ ] All review comments addressed and resolved
- [ ] Architecture consistency verified
- [ ] No code review anti-patterns detected

## Deployment Readiness
- [ ] Backward compatibility maintained (or migration plan provided)
- [ ] Database migrations tested
- [ ] Configuration requirements documented
- [ ] Deployment runbook reviewed
- [ ] Rollback plan documented
 Professional SDD Implementation Roadmap

**Immediate (Next Release):**
- ✅ Copy Definition of Done to PR/MR description template
- ✅ Start capturing Lessons Learned after each feature
- ✅ Reference Decision Records when making architecture choices

**Next Sprints:**
- [ ] Create first ADR (architecture decisions made to date)
- [ ] Generate traceability matrices for 2-3 major features
- [ ] Implement NFR specifications in planning documents
- [ ] Set up weekly lessons learned reviews

**Quarterly:**
- [ ] Review reference architecture guidance
- [ ] Update templates based on team feedback
- [ ] Establish change control process for specifications
- [ ] Build acceptance test framework (Cucumber setup)
```
---

## Quick Reference: Using Professional Templates

| Template | Location | When Used | Owner |
|---|---|---|---|
| Definition of Done | `definition-of-done.md` | Before each merge | Tech Lead |
| Architecture Patterns | `reference-architecture.md` | During design reviews | Architecture Team |
| Traceability | `traceability.md` | Before release | QA Lead |
| ADRs | `adr/ADR-{number}.md` | After architecture decision | Tech Lead |
| NFR Specifications | `nfr-specifications.md` | During planning | Product + Tech Lead |
| Risk Management | `risks-and-dependencies.md` | Weekly reviews | Project Manager |
| Change Management | `VERSIONING-GUIDE.md` | When spec changes | Product Owner |
| Acceptance Tests | `acceptance-tests/*.gherkin` | After accepting criteria defined | QA |
| Lessons Learned | `lessons-learned/YYYY-MM-DD-*.md` | Post-feature review | Team collective |
## Metrics & Data 📊

| Metric | Value | Target |
|--------|-------|--------|
| Code Coverage | 85% | 80% |
| P95 Response Time | 95ms | 100ms |
| Time to First Test | 2.8 days | 3 days |
| Rework % | 0% | < 5% |

## Recommendations for Next Features 📝

1. ✅ Adopt JWT-first approach for stateless APIs
2. ✅ Use GitHub Copilot Chat for code generation (with review)
3. ✅ Include performance benchmarks in acceptance criteria
4. ⚠️ Add security architect review for auth features
5. ⚠️ Allocate more time for integration test setup

**Benefits:** Organizational learning, process improvement, captures tacit knowledge.

---

### Summary: Gap Analysis for Mature SDD

| Enhancement | Priority | Effort | Impact | Timeline |
|---|---|---|---|---|
| **Traceability Matrix** | High | Medium | 🟢🟢🟢 | Now |
| **Acceptance Test Specs** | High | High | 🟢🟢🟢 | Next sprint |
| **Architecture Decision Records** | Medium | Low | 🟢🟢 | Ongoing |
| **NFR Specifications** | High | Medium | 🟢🟢🟢 | Now |
| **Risk Management** | Medium | Medium | 🟢🟢 | Next sprint |
| **API Documentation Standard** | High | Medium | 🟢🟢🟢 | Now |
| **Change Management** | Medium | Low | 🟢🟢 | Ongoing |
| **Reference Architecture** | Medium | High | 🟢🟢 | 2 sprints |
| **Definition of Done** | High | Low | 🟢🟢🟢 | Now |
| **Lessons Learned Process** | Low | Low | 🟢 | Ongoing |

---

## Troubleshooting & Best Practices

### Common Issues & Solutions

#### Issue 1: Specification is too vague

**Problem:** Implementation team unsure about requirements

**Solution:**
```
@workspace Review this specification for clarity and completeness:
/.spec/specs/{feature-name}.md


✅ **Define non-functional requirements**
- Performance targets
- Security requirements
- Scalability expectations
- Compliance needs

✅ **Identify dependencies**
- What else must be done first?
- What could block this?
```
---

#### For Planners & Architects

✅ **Map specification to architecture layers**
- Which classes/packages need changes?
- What's the design pattern?

✅ **Include database design**
- If data model changes, show schema
- Include indexes and relationships

✅ **Document trade-offs**
- Why this approach over alternatives?
- What are the compromises?

✅ **Plan for testing**
- Unit test scope
- Integration test scope
- Performance test plan

---

#### For Implementers

✅ **Code to the specification, not the guess**
- If something seems wrong, ask clarifying questions
- Reference spec 😎 in code comments
- Test against acceptance criteria

✅ **Test-driven development (recommended)**
- Write test matching acceptance criteria first
- Write code to pass test
- Validates spec interpretation

✅ **Keep tasks small**
- One task = one focused change
- Easier to review, test, debug
- Track progress better

---

#### For Reviewers

✅ **Check specification alignment**
- Does code implement requirements?
- Are acceptance criteria met?
- Any unspecified features?

✅ **Verify quality standards**
- Code follows Constitution
- Tests meet coverage targets
- Performance acceptable

✅ **Validate architecture**
- Correct layer separation?
- Consistent patterns?
- Proper dependency injection?

---

### FAQ

**Q: How much time should I spend on specification vs. implementation?**

A: Summary: Professional SDD Templates

| Template | Status | Purpose | Priority | Start |
|---|---|---|---|---|
| **Definition of Done** | ✅ Created | Quality gate checklist | 🔴 High | Now |
| **Reference Architecture** | ✅ Created | Design patterns guide | 🔴 High | Now |
| **Traceability Matrix** | ✅ Created | Requirements coverage | 🔴 High | Next release |
| **ADR Template** | ✅ Created | Architecture decisions | 🟡 Medium | When deciding architecture |
| **NFR Specifications** | ✅ Created | Measurable targets | 🔴 High | During planning |
| **Risk Management** | ✅ Created | Dependency tracking | 🟡 Medium | During planning |
| **Versioning Guide** | ✅ Created | Change management | 🟡 Medium | When spec changes |
| **Acceptance Tests** | ✅ Created | Gherkin BDD scenarios | 🟢 Low | Optional (advanced) |
| **Lessons Learned** | ✅ Created | Post-feature learning | 🟢 Low | After each feature |

**Implementation Timeline:**
- **Week 1:** Copy DoD to PR template, reference patterns document
- **Week 2:** Start capturing lessons learned after feature completion
- **Week 3:** Create first ADR for major architectural decision
- **Month 2:** Generate traceability matrices for major features
- **Ongoing:** Update templates based on team feedback
4. Get stakeholder approval for revised deadline
5. Update implementation and tests

---

**Q: Can I skip the plan if the feature is very small?**

A: Minimum SDD structure (even for small features):
- ✅ **Must have:** Specification with acceptance criteria
- ✅ **Must have:** Test cases covering acceptance criteria
- ⚠️ **Optional:** Formal plan document (can be very brief)
- ⚠️ **Optional:** Task breakdown (sometimes just spec is enough)

---

**Q: How do I handle requirements that conflict?**

A: Escalate to stakeholder in specification review:

```
@workspace Review this specification for conflicting requirements:
/.spec/specs/{feature-name}.md

Identify:
1. Requirements that contradict each other
2. Performance vs. functionality trade-offs
3. Missing prioritization language

Suggest solutions or clarifications needed from stakeholders.
```

---

**Q: What's the minimum test coverage expectation?**

A: Per Constitution: **80% minimum code coverage**

However:
- **Domain layer:** 90%+ (critical business logic)
- **Application layer:** 85%+ (use case orchestration)
- **Infrastructure:** 70%+ (framework-heavy, harder to test)
- **Presentation:** 75%+ (mostly validation/marshalling)

---

## Conclusion

Your SDD process provides excellent foundation with:
- ✅ Clear specification templates
- ✅ Architectural governance (Constitution)
- ✅ Layered implementation structure
- ✅ Systematic task breakdown

### To Elevate to "Professional Grade"

**Immediate (Next Sprint):**
1. Create Definition of Done document
2. Add traceability matrix template
3. Document your risk management process

**Short-Term (Next 2 Sprints):**
1. Implement ADR (Architecture Decision Records)
2. Create API documentation standard
3. Establish lessons-learned capture process

**Medium-Term (Next 3-6 Months):**
1. Build reference architecture guide
2. Create reusable component library
3. Establish metrics/KPIs for process health

Your team has the right structure; now it's about capturing and scaling your domain knowledge.

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Prepared By:** GitHub Copilot  
**Status:** Ready for Review  

For questions or improvements to this guide, please reference this document in your PR/issue.
