# Risks & Dependencies Management

**Purpose:** Proactively identify and track risks and dependencies that could impact feature delivery.

**Last Updated:** 2026-04-07  
**Review Frequency:** Weekly during active development

---

## Overview

### Why This Matters

- **Early Warning:** Identify blockers before they impact schedule
- **Mitigation:** Have contingency plans in place
- **Communication:** Keep stakeholders informed of risks
- **Learning:** Capture what we learned for future features

---

## Template: Feature Risks & Dependencies

```markdown
# Feature: [Feature Name]

**Specification:** `/.spec/specs/{feature-name}.md`  
**Planned Duration:** [X weeks]  
**Target Completion:** [YYYY-MM-DD]

## Dependencies

### Internal Dependencies (Same Team)
- [ ] Task/Deliverable: [Description]
  - **Owner:** [Name]
  - **Due Date:** [YYYY-MM-DD]
  - **Status:** Not Started | In Progress | Blocked | Complete
  - **Impact if Delayed:** Blocks [specific task]

### External Dependencies (Other Teams/Systems)
- [ ] Task/Deliverable: [Description]
  - **Owner:** [External Team/Person]
  - **Due Date:** [YYYY-MM-DD]
  - **Status:** Not Started | In Progress | Blocked | Complete
  - **Contact:** [Email/Slack]
  - **Impact if Delayed:** Blocks [specific task]

### Infrastructure Dependencies
- [ ] Resource/Service: [Description]
  - **Responsible:** [Team/Vendor]
  - **Required By:** [YYYY-MM-DD]
  - **Status:** Not Started | Approved | Provisioned
  - **Impact if Missing:** [Blocks what]

## Risks

### High Priority Risks

| Risk ID | Risk | Impact | Probability | Severity | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|---|
| R-001 | [Risk description] | [What happens if it occurs] | High/Med/Low | Critical/High/Med | [Action plan] | [Name] | Active |

### Medium Priority Risks

[Risk tracking table]

### Low Priority Risks

[Risk tracking table]

## Critical Path

The sequence of tasks that determines the minimum project duration:

1. **Task 1** (3 days) → CRITICAL
   - Blocks: Task 2, Task 3
   - Depends: Task X (from other team)
   - Risk: Database setup delay

2. **Task 2** (2 days)
   - Blocks: Task 4
   - Depends: Task 1
   - Risk: Performance testing complexity

## Mitigation Strategies

### For High-Impact Risks:

**Risk Example:** Database performance insufficient

**Mitigation Plan:**
1. Early: Run performance tests on proposed schema (Week 1)
2. If insufficient: Pre-allocate time for indexing optimization
3. Fallback: Implement caching layer (Redis)
4. Contingency: Delay other features if needed

**Responsible:** [Name]  
**Status:** ⚠️ In Progress
```

---

## Example: Leave Request Workflow

# Feature: Leave Request Workflow

**Specification:** `/.spec/specs/leave-request-workflow.md`  
**Planned Duration:** 3 weeks  
**Target Completion:** 2026-04-30

---

## Dependencies

### Internal Dependencies

- [ ] **Database Migration**
  - **Description:** Create leave_requests, leave_history, approval_chain tables
  - **Owner:** DB Team / Infrastructure Lead
  - **Due Date:** 2026-04-10
  - **Status:** In Progress
  - **Impact if Delayed:** Blocks repository implementation (2 days)

- [ ] **Email Service Integration**
  - **Description:** Email template setup for leave notifications
  - **Owner:** Platform Team
  - **Due Date:** 2026-04-12
  - **Status:** Not Started
  - **Impact if Delayed:** Blocks features that send notifications (1 day)

- [ ] **User Role Enhancement**
  - **Description:** Add "manager" and "hr_admin" role types with permissions
  - **Owner:** Auth Team
  - **Due Date:** 2026-04-08
  - **Status:** Complete ✅
  - **Impact if Delayed:** Would have blocked authorization design

### External Dependencies

- [ ] **HR System API Access**
  - **Description:** API credentials and documentation for HR system integration
  - **Owner:** HR Department / 3rd Party Vendor
  - **Contact:** hr-integration@vendor.com
  - **Due Date:** 2026-04-15
  - **Status:** Blocked (waiting for security review)
  - **Impact if Delayed:** Can't verify manager authority (3 days delay potential)
  - **Workaround:** Mock HR system for development and testing

- [ ] **Payment System Integration**
  - **Description:** Calculate hourly rate impact for leave deduction
  - **Owner:** Payroll Team
  - **Due Date:** 2026-04-18
  - **Status:** Not Started
  - **Impact if Delayed:** Blocks payroll integration (can be in Phase 2)

### Infrastructure Dependencies

- [ ] **Redis Cache Instance**
  - **Description:** Redis deployment for caching manager role assignments
  - **Responsible:** DevOps Team
  - **Required By:** 2026-04-20
  - **Status:** Provisioning approved, setup scheduled
  - **Impact if Missing:** Performance optimization skip-able (fallback to DB queries)

- [ ] **Load Testing Environment**
  - **Description:** Production-like environment to test 1000 req/sec target
  - **Responsible:** QA Infrastructure
  - **Required By:** 2026-04-25
  - **Status:** Not Started
  - **Impact if Missing:** Can't verify performance targets pre-release

---

## Risks

### High Priority Risks ⛔

| Risk ID | Risk | Impact | Probability | Severity | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|---|
| R-001 | HR system API delayed | Can't complete manager authority check | High (50%) | Critical | Use mocked HR system for Phase 1 dev; plan Phase 2 integration | John Smith | 🟡 Active |
| R-002 | Performance testing reveals N+1 queries | Authorization checks too slow | Medium (35%) | High | Pre-production load test Week 2; eager load query optimization | Jane Doe | 🟡 Active |
| R-003 | Scope creep: "Add leave balance tracking" | Schedule delay 1 week | High (60%) | High | Strict scope freeze after spec approval; changes to Phase 2 | Project Lead | 🟡 Active |

### Medium Priority Risks ⚠️

| Risk ID | Risk | Impact | Probability | Severity | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|---|
| R-004 | Email service downtime during testing | Can't test notifications | Medium (30%) | Medium | Set up local email mock; test email retry logic | Email Team Liaison | 🟢 Monitoring |
| R-005 | Database replication lag | Manager sees outdated leave requests | Low (20%) | Medium | Configure synchronous replication; add consistency checks | DB Admin | 🟢 Configured |
| R-006 | Timezone handling bugs | Leave dates calculated incorrectly | Medium (40%) | High | Use UTC internally; comprehensive timezone tests Week 1 | Backend Lead | 🟡 Testing |

### Low Priority Risks 🟢

| Risk ID | Risk | Impact | Probability | Severity | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|---|
| R-007 | API documentation generation fails | Dev doc incomplete | Low (15%) | Low | Manual documentation fallback | Technical Writer | 🟢 Low Risk |
| R-008 | Code complexity exceeds limits | Code review cycles extend | Low (25%) | Low | Refactoring if complexity > 10; pair programming for complex areas | Tech Lead | 🟢 Design |

---

## Critical Path

The sequence of tasks that, if delayed, will push the entire delivery date:

```
┌─ Database Schema ─────────┐
│  (3 days) [CRITICAL]      │
│  Blocks: Repositories      │
│  Due: 2026-04-10          │
├─ User Roles Setup ─────────┤
│  (2 days) [CRITICAL]       │
│  Blocks: Authorization     │
│  Due: 2026-04-08           │ ✅ COMPLETE
└─────────────────────────────┘
        ↓
    ┌───────────────────────────┐
    │ Domain Model Implementation│
    │ (5 days) [CRITICAL]       │
    │ Blocks: Services          │
    │ Due: 2026-04-15           │
    └───────────────────────────┘
        ↓
    ┌─── Application Services ──┐
    │ (4 days) [CRITICAL]       │
    │ Blocks: Controllers       │
    │ Due: 2026-04-19           │
    └───────────────────────────┘
        ↓
    ┌───── Integration Tests ────┐
    │ (3 days) [CRITICAL]       │
    │ Blocks: Release           │
    │ Due: 2026-04-22           │
    └───────────────────────────┘
        ↓
    Email Integration (Parallel, non-critical)
    └─→ Can slip 2 days without affecting main path
```

### Critical Path Summary

- **Minimum Duration:** 17 calendar days (5+4+3+2+3)
- **Current Schedule:** 21 calendar days (includes buffers)
- **Slack:** 4 days (buffer for unexpected issues)
- **Key Bottleneck:** Database schema approval (Day 1-3)

---

## Dependency Timeline

```
Week 1 (Apr 7-11)
├─ User Roles Ready ..................... Apr 8 ✅
├─ Database Schema Migration............. Apr 10 ⏳ (In Progress)
└─ HR API Credentials Request............. Sent ⏳

Week 2 (Apr 14-18)
├─ Database Migration Complete............ Apr 10 ← CRITICAL
├─ Domain Model Work..................... Apr 14-18
├─ HR API Available...................... Apr 15 ⏳ (Waiting)
└─ Email Service Ready................... Apr 12 ✅

Week 3 (Apr 21-25)
├─ Application Services Complete.......... Apr 19
├─ Integration Tests Started............. Apr 20 ← CRITICAL
├─ Performance Testing Environment Ready.. Apr 25 ⏳
└─ Load Test Run........................ Apr 25

Week 4 (Apr 28-May 2)
├─ All Tests Complete................... Apr 30
├─ Code Review Final Approval............ May 1 ← RELEASE
└─ Release Candidate Ready.............. May 2
```

---

## Escalation Path

**If a critical dependency is blocked:**

1. **Notification** (immediately)
   - Slack: @project-lead @engineering-manager
   - Email: stakeholders@company.com
   - Update this document

2. **Assessment** (within 2 hours)
   - Impact on schedule
   - Possible workarounds
   - Fallback plans

3. **Decision** (within 4 hours)
   - Proceed with workaround? Delay? Escalate?
   - Communicate new timeline to stakeholders
   - Update critical path

4. **Action** (within 8 hours)
   - Execute decision
   - Daily check-ins if still critical

---

## Mitigation Strategies

### Strategy 1: Early Database Migration

**Risk:** Database setup delays feature implementation  
**Mitigation:**
1. Start database migration request NOW (not Day 8)
2. Test schema in dev before requesting production
3. Prepare rollback plan if migration fails
4. Have alternate schema if needed

**Responsible:** Database Team Lead  
**Target Date:** Approved by 2026-04-08

---

### Strategy 2: Mock External Services

**Risk:** HR system API not available on time  
**Mitigation:**
1. Develop with mocked HR Service interface
2. Inject mock implementation for Phase 1 testing
3. Swap to real service in Phase 2
4. Comprehensive integration tests for real service

**Implementation:**
```java
// Mock interface (same for real and mock implementations)
public interface HRServiceClient {
    Manager getManagerForEmployee(String employeeId);
}

// Use dependency injection to swap implementations
@Bean
public HRServiceClient hrServiceClient(@Value("${hr.service.enabled}") boolean enabled) {
    return enabled ? new HRServiceClientReal() : new HRServiceClientMock();
}
```

**Responsible:** Backend Team Lead  
**Target Date:** Mock ready by 2026-04-10

---

### Strategy 3: Performance Testing Early

**Risk:** Performance tests reveal architecture issues too late  
**Mitigation:**
1. Early performance test of database queries (Week 1)
2. Load test authorization checks (Week 2)
3. Full integration test (Week 3)
4. Fix issues as discovered, don't batch for end

**Responsible:** QA Lead + Architecture Lead  
**Target Date:** First test run by 2026-04-12

---

### Strategy 4: Change Control for Scope Creep

**Risk:** New requirements added mid-development  
**Mitigation:**
1. Specification freeze after approval
2. Any new requirement = formal change request
3. Change requests evaluated for impact
4. Priority: Fix bugs > Required changes > Nice-to-haves
5. If approved, add to Phase 2 or extend timeline

**Process:**
- Submit change request in GitHub issue
- Link to specification section affected
- Assessment: time, resource, risk impact
- Approval required if timeline/cost affected

**Responsible:** Project Manager + Tech Lead

---

## Weekly Risk Review

### Questions to Answer Every Monday

1. **Are any High-risk items status changed?**
2. **Are any critical path tasks on track?**
3. **Any new dependencies identified?**
4. **Any blocked blockers escalated?**
5. **Should mitigation strategies be updated?**

### Review Meeting

- **Time:** Every Monday 10:00 AM
- **Duration:** 30 minutes
- **Attendees:** Tech Lead, Project Manager, Team Leads
- **Agenda:** Review this document, update status, identify escalations

---

## Lessons Learned (Updated Post-Feature)

Use this section to capture what we learned about risk management for this feature:

| What | Lesson | Action for Next Time |
|---|---|---|
| HR API delay | We underestimated vendor response time | Add 1-week buffer for external integrations |
| Database optimization | N+1 queries discovered late | Load test queries in Week 1, not Week 3 |
| Timezone bugs | Timezone handling is complex | Add timezone tests to definition of done |

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07  
**Next Review:** 2026-04-14  
**Prepared By:** Project Lead / Tech Lead
