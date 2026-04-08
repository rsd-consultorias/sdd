# SDD Project Time Estimation Guide

## How to Estimate Project Execution Time Using Spec-Driven Development

As a business analyst working with user stories in a Spec-Driven Development (SDD) process, use this framework to estimate project execution time.

---

## 1. Break Project Into Features (from User Stories)

Each user story maps to one feature in your SDD process. For each feature, the project goes through 4 phases:

| Phase | Duration | Purpose |
|-------|----------|---------|
| **Specification** | 1-3 days | Define WHAT & WHY (requirements, acceptance criteria) |
| **Planning** | 1-2 days | Define HOW & WHEN (technical design, task breakdown) |
| **Implementation** | Variable | Write code across 4 layers (Domain → App → Infra → Presentation) |
| **Validation** | 1-2 days | Testing, code review, compliance verification |

**Base Formula:**
```
Feature Time = 2-7 days (Spec + Planning + Validation) + X days (Implementation)
```

---

## 2. Estimate Implementation Time by Complexity

Use your task breakdown (`.spec/tasks/{feature}.tasks.md`) to estimate implementation. Consider:

- **Task Complexity Sizing**: S (1-2 days) | M (3-5 days) | L (6-10 days) | XL (10+ days)
- **Architecture Layers**: Each layer adds 1-3 days
  - Domain Layer: 1-2 days
  - Application Layer: 1-2 days
  - Infrastructure Layer: 1-3 days (increases with external integrations)
  - Presentation Layer: 1-2 days
- **External Integrations**: Add 2-5 days per integration
- **Testing & Documentation**: Budget 20-30% of implementation time

---

## 3. Account for Dependencies

From your task breakdown, identify:
- **Sequential tasks** (can't start until previous completes) → add time in series
- **Parallel tasks** (can run simultaneously) → don't add time, just track longest chain
- **Blockers** (dependencies between features) → delay subsequent features

Document these in your task dependency matrix to identify the critical path.

---

## 4. Apply Team Capacity Factors

Adjust estimates based on:
- **Team experience** with Spring Boot/Java
  - First feature: +30% buffer
  - Subsequent features: -10% (learning curve advantage)
- **Team size** (single developer vs. parallel teams)
  - Single developer: features sequential
  - Multiple developers: can parallelize phases across features
- **Availability** (% of time allocated to project)
  - 50% allocation: double the estimate
  - 25% allocation: quadruple the estimate

---

## 5. Total Project Estimate

```
Total Project Time = Σ(Feature Time) + Integration Testing + Deployment Buffer

Example for 5 user stories:
- Feature 1: 3 (spec+plan+val) + 5 (impl) = 8 days
- Feature 2: 2 + 6 = 8 days  
- Feature 3: 2 + 4 = 6 days
- Feature 4: 2 + 3 = 5 days
- Feature 5: 2 + 7 = 9 days
──────────────────────────────────
Subtotal: 36 days
+ Integration/E2E testing: 3 days
+ Buffer (20%): 8 days
──────────────────────────────────
Total: ~47 days
```

---

## 6. Gather Input From SDD Documents

To refine estimates:

- **`.spec/plans/{feature}.plan.md`** → Look for "Implementation Phases" section with time estimates provided by developers
- **`.spec/tasks/{feature}.tasks.md`** → Check "Time Estimates" section for per-task complexity sizing (S/M/L/XL)
- **`.spec/constitution.md`** → Review architectural constraints that affect implementation complexity
- **`.spec/specs/{feature}.md`** → Assess functional and non-functional requirements for scope

---

## 7. Validation Checklist

Before finalizing estimates, ensure:

- [ ] All user stories have approved specifications (status: "Approved")
- [ ] Each specification is linked to a plan with technical design
- [ ] Plans have identified all external dependencies and risks
- [ ] Team members have estimated task complexity (S/M/L/XL)
- [ ] Buffer (15-20%) added for unknowns and risk mitigation
- [ ] External dependency timelines assessed (3rd-party APIs, services)
- [ ] Team capacity and availability factored in
- [ ] Integration testing and deployment time estimated separately

---

## Quick Reference: Phase Duration Ranges

| Scope | Duration | User Stories |
|-------|----------|-------------|
| **Small Feature** | 2-3 weeks | 1-2 stories |
| **Medium Feature** | 4-6 weeks | 3-5 stories |
| **Large Feature** | 7-10+ weeks | 6+ stories |

---

## SDD Phase Sequence (When Using Multiple Features)

For optimal resource utilization, phases can overlap:

```
Timeline example with 3 features and 2 developers:

Week 1-2:  Feature 1 (Spec + Planning)      Developer A
Week 1-2:                                     Developer B → Feature 2 (Spec)
Week 2-3:  Feature 1 (Implementation)       Developer A
Week 2-4:                                     Developer B → Feature 2 (Planning + Implementation)
Week 4-5:  Feature 1 (Testing + Review)     Developer A
Week 4-6:                                     Developer B → Feature 2 (Testing + Review)
```

This overlapping approach reduces total project time compared to sequential execution.

---

## Key Success Factors for Accurate Estimates

1. **Specification Completeness**: Incomplete specs lead to +30-50% time overruns
2. **Dependency Clarity**: Unclear dependencies cause delays; map these explicitly
3. **Team Experience**: First sprint is slower; plan accordingly
4. **External Factors**: Third-party service delays, environment setup, etc.
5. **Risk Assessment**: Include contingency for identified risks from `.spec/risks-and-dependencies.md`

---

## Adjusting Estimates During Project Execution

As the project progresses:
- Update estimates weekly based on actual task completion rates
- Track velocity (story points or days completed per sprint)
- Adjust remaining features based on team's observed capacity
- Re-assess risks and dependencies from `.spec/risks-and-dependencies.md`

---

## References

For more details on SDD process:
- Full SDD Guide: `.spec/README.md`
- Architectural Standards: `.spec/constitution.md`
- Non-Functional Requirements: `.spec/nfr-specifications.md`
- Risk Management: `.spec/risks-and-dependencies.md`
