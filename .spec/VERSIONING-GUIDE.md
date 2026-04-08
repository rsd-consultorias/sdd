# Change Management & Versioning Guide

**Purpose:** Manage specification changes and maintain clear version history.

**When to Use:** When requirements change mid-feature or post-release.

---

## Specification Versioning Template

### Header (Every Spec File)

```markdown
# Feature Spec: [Feature Name]

**Version:** 1.0  
**Status:** Draft | In Review | Approved | In Development | Complete  
**Last Updated:** [YYYY-MM-DD]  
**Owner:** [Team/Person]  

---

## Version History

| Version | Date | Author | Status | Summary |
|---|---|---|---|---|
| 1.0 | 2026-04-07 | John Doe | Approved | Initial specification |
| 1.1 | 2026-04-09 | Jane Smith | In Development | Added hourly deduction calculation |
| 1.2 | 2026-04-12 | John Doe | In Development | Clarified manager escalation rules |
```

---

## Change Log Template

```markdown
## Change Log

### Version 1.1 Changes
**Date:** 2026-04-09  
**Author:** Jane Smith  
**Reason:** Stakeholder feedback from payroll team  

#### Modified Requirements:
- **FR-2 Modified:** "Manager approves leave" → Now also calculates hourly deduction
- **NFR-3 Added:** "Deduction calculation ≤ 10ms"

#### Impact Assessment:
- **Domain Impact:** Medium (new value object: HourlyDeduction)
- **Timeline Impact:** +1-2 days (new calculation logic)
- **Test Impact:** New test scenarios for deduction calculation
- **Database Impact:** No schema changes

#### Affected Tasks:
- ✏️ Domain: Add HourlyDeduction value object (new task)
- ✏️ Application: LeaveApprovalService deduction logic (extended)
- ✏️ Testing: Add deduction calculation tests (new task)

#### Approval:
- [ ] Product Owner: Approved on 2026-04-09
- [ ] Tech Lead: Approved on 2026-04-09 (reviewed impact)
- [ ] Estimated +2 days added to schedule

---

### Version 1.2 Changes
**Date:** 2026-04-12  
**Author:** John Doe  
**Reason:** Clarification based on manager testing  

#### Clarified Requirements:
- **FR-3 Clarified:** "HR admin can override" → Now includes escalation chain:
  - Level 1: Department Manager
  - Level 2: HR Manager (if L1 rejects)
  - Level 3: HR Admin (final override)

#### Impact Assessment:
- **Domain Impact:** Low (already supports decision reasons)
- **Design Impact:** Medium (escalation workflow added)
- **Timeline Impact:** No change (within Phase 2 design scope)
- **Test Impact:** New integration tests for escalation path

#### Approval:
- [ ] Product Owner: Approved on 2026-04-12
- [ ] No timeline impact
```

---

## Change Request Process

### When Requirements Change:

**Step 1: Document the Change (24 hours)**

```markdown
# Change Request #[NUMBER]

**Submitted By:** [Name]  
**Date Submitted:** [YYYY-MM-DD]  
**Status:** Submitted | Under Review | Approved | Rejected

## Change Description
[Detailed description of what's changing and why]

## Impact Analysis
- Domain impact: [High/Medium/Low - why]
- Timeline impact: [+X days or no impact]
- Risk/complexity: [Assessment]
- Affected components: [List of changed entities/services]

## Alternatives Considered
- Option A: [Description] → Rejected because [reason]
- Option B: [Description] → Rejected because [reason]

## Recommended Approach
[Recommended implementation approach]

## Questions for Stakeholder
1. [Question]
2. [Question]
```

**Step 2: Assessment (2 days max)**

Product Owner + Tech Lead review:
- Does this fit in current timeline?
- Should this be Phase 2 instead of Phase 1?
- What's the impact on existing implementation?

**Step 3: Decision (2 days max)**

Options:
- ✅ **Approved in Phase 1** → Add to current implementation
- ✅ **Approved for Phase 2** → Create separate feature spec
- ❌ **Rejected** → Return to requester with explanation
- 🔄 **Deferred** → Re-evaluate after feature complete

**Step 4: Implementation**

If approved:
1. Update specification version (e.g., 1.0 → 1.1)
2. Add to Change Log
3. Update affected task breakdown
4. Update timeline if needed
5. Communicate to team
6. Continue development

---

## Version Numbering Convention

```
Version: X.Y

X = Major version
  - Incremented when significant requirements change
  - Example: Initial spec → Phase 2 added major new use case

Y = Minor version
  - Incremented when clarifications or minor additions
  - Example: FR clarified, new acceptance criterion added

Reset Y to 0 when X increments
Example progression: 1.0 → 1.1 → 1.2 → 2.0 → 2.1
```

---

## Status Definitions

| Status | Meaning | Who Can Change | Next State |
|---|---|---|---|
| **Draft** | Initial version, work in progress | Author | In Review |
| **In Review** | Ready for stakeholder review | Author | Approved / Draft (if revisions needed) |
| **Approved** | Stakeholder approved, team can start | Tech Lead | In Development |
| **In Development** | Team actively building | Tech Lead (for versions) | Complete |
| **Complete** | Released to production | Tech Lead | (None - archive) |

---

## Impact Analysis Checklist

When evaluating a change request:

- [ ] **Requirements Impact**
  - [ ] Does this add new FR? (complexity +++)
  - [ ] Does this modify existing FR? (risk: breaking changes)
  - [ ] Does this add new NFR? (performance/security implications)

- [ ] **Design Impact**
  - [ ] Affects domain entities? (highest impact)
  - [ ] Affects services? (medium impact)
  - [ ] Affects controllers only? (lowest impact)

- [ ] **Database Impact**
  - [ ] Requires schema changes?
  - [ ] Can use existing tables?
  - [ ] Migration complexity? (backward compatible?)

- [ ] **Testing Impact**
  - [ ] New test scenarios required?
  - [ ] How many existing tests need updating?

- [ ] **Timeline Impact**
  - [ ] Days to implement: [_____]
  - [ ] Days to test: [_____]
  - [ ] Total slip: +[_____] days
  - [ ] Is this acceptable?

---

## Example: Change Management in Action

### Scenario: Mid-Development Change Request

**Initial Plan:**
- Leave requests: Employee → Manager → Approve/Reject

**Day 10 (Mid-implementation):** Stakeholder requests escalation

**Change Request Submitted:**

```markdown
# Change Request #CR-001

**Submitted By:** HR Director  
**Date:** 2026-04-12

## Change Description
Need escalation path for rejected leaves:
- If Manager rejects → Employee can appeal to HR Manager
- If HR Manager rejects → Can escalate to HR Director (final decision)

## Impact Analysis
- Domain Impact: MEDIUM (new approval chain)
- Timeline: +1 day (add escalation logic + tests)
- Risk: Could delay release by 1 day
- Components: LeaveRequest aggregate, ApprovalChain service
```

**Assessment (2 hours):**
- Tech Lead + Product Owner review
- Conclusion: Can fit in timeline with priority shift

**Decision (1 hour):**
- ✅ Approved for Phase 1 (released as v1.1)
- Schedule impact: +1 day (acceptable)

**Implementation (1 day):**

Spec updated:
```markdown
# Feature Spec: Leave Request Workflow
**Version:** 1.1 (updated 2026-04-12)

## Version History
| Version | Date | Author | Change |
|1.0|2026-04-07|Product Owner|Initial|
|1.1|2026-04-12|Product Owner|Added escalation chain|

## Change Log - Version 1.1
**Added FR-4:** Escalation for rejected leaves
- Employee rejected by Manager → Appeal to HR Manager
- Rejected by HR Manager → Final escalation to HR Director

**New NFR:** Escalation notification ≤ 100ms
```

Task updated:
```markdown
- [ ] **Escalation Service** (NEW)
  - Implements: FR-4, NFR-4
  - Estimate: 1 day
  - Path: LeaveEscalationService.java
```

Team notified:
- PR description references CR-001 and version 1.1
- Timeline extended from 2026-04-21 to 2026-04-22
- Communication sent to stakeholders

---

## Post-Release Versioning

### Bug Fixes (Patch Release)

```markdown
# Feature Spec: Leave Request Workflow
**Version:** 1.0.1 (patch)

## Bug Fix: Escalation notification timestamp incorrect

**Issue:** Escalation emails sent with wrong date  
**Root Cause:** Timezone handling in NotificationService  
**Fix:** Use UTC consistently  
**Release Date:** 2026-04-25  
**Impact:** Patch, no spec change needed
```

### Minor Clarifications (Minor Release)

```markdown
# Feature Spec: Leave Request Workflow
**Version:** 1.1.1

## Clarification: "HR Manager" role definition

**Clarification:** HR Manager = HR staff with approval authority  
(Previously: ambiguous)

**Impact:** No code change, documentation only
```

### Major Feature Addition (Major Release)

```markdown
# Feature Spec: Leave Request Workflow
**Version:** 2.0

## Major Addition: Batch Leave Approval

**New FR-5:** HR Manager can approve/reject multiple requests at once

**Change Log:** See v2.0 section
```

---

## Communicating Version Changes

### Email Template (for significant changes)

```
Subject: Specification Change - [Feature Name] - v1.0 → v1.1

Team,

The specification for [Feature Name] has been updated:

VERSION: 1.0 → 1.1
DATE: 2026-04-12
REASON: Stakeholder feedback / Clarification / Scope adjustment

CHANGES:
- Added: [New requirement]
- Modified: [Changed requirement]
- Impact: +1 day on delivery (new: 2026-04-22)

NEXT STEPS:
- Specification file: /.spec/specs/{feature-name}.md (v1.1)
- Planning update needed for tasks by: 2026-04-13
- Affected tasks: [Task list]

REVIEW & QUESTIONS:
Please review changes by EOD 2026-04-13.
Contact [Tech Lead] with questions.

---
Details: /.spec/specs/{feature-name}.md (Version History section)
```

---

## Document Version Management Best Practices

- ✅ **Version every spec** from v1.0 onward
- ✅ **Maintain change log** so history is clear
- ✅ **Link changes to code commits** (reference in PR)
- ✅ **Communicate major changes** to stakeholders
- ✅ **Archive old versions** (git history maintains them)
- ✅ **Test all changed requirements** thoroughly
- ❌ **Don't overwrite previous versions** (maintain history)
- ❌ **Don't make changes without approval** (process prevents rework)

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-07
