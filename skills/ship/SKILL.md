---
name: ship
description: "End-to-end delivery pipeline orchestrating 5 core skills: grilling (stress-test requirements), to-spec (synthesize spec), to-tickets (tracer-bullet ticket decomposition), implement (TDD & execution), and code-review (two-axis standards & spec review). Trigger with /ship or 'ship this'."
---

# Ship Pipeline (`/ship`)

A disciplined, end-to-end software delivery pipeline designed to take an idea from ambiguous intent to verified, production-ready code.

```text
┌─────────────┐     ┌─────────────┐     ┌────────────────┐     ┌─────────────┐     ┌───────────────┐
│ 1. Grilling │ ──> │ 2. To-Spec  │ ──> │  3. To-Tickets │ ──> │4. Implement │ ──> │5. Code-Review │
│ (Interview) │     │ (Synthesize)│     │(Decomposition) │     │ (TDD/Build) │     │(Two-Axis QA)  │
└─────────────┘     └─────────────┘     └────────────────┘     └─────────────┘     └───────────────┘
```

---

## Pipeline Execution Overview

The `/ship` pipeline runs sequentially through five distinct phases. Do NOT skip phases unless explicitly directed by the user.

| Phase | Skill | Primary Goal | Gate / Exit Condition |
| :--- | :--- | :--- | :--- |
| **Phase 1** | `grilling` | Stress-test thinking, clarify edge cases, map design tree | Frontier settled; no open architectural ambiguities |
| **Phase 2** | `to-spec` | Synthesize context & codebase seams into formal spec | Spec written and approved by user |
| **Phase 3** | `to-tickets` | Decompose spec into tracer-bullet, dependency-ordered tasks | Tickets generated with explicit blocking edges |
| **Phase 4** | `implement` | Execute tickets in order using TDD & verification loops | Tests pass, typechecks clean, changes committed |
| **Phase 5** | `code-review` | Two-axis review: Standards (repo conventions) & Spec (parity) | Review findings addressed and ready for merge |

---

## Detailed Phase Protocols

### Phase 1: Grilling (`grilling`)
**Goal:** Reach total shared understanding before touching or drafting code.

1. Map the user's request as a **design tree**: every decision branches into the decisions hanging off it.
2. Identify the **frontier**: questions whose prerequisites are already settled that can be asked *now*.
3. Present questions in structured rounds:
   ```markdown
   ❓ **Q1** - **<question title>**: <question body with trade-offs/options>
   ➡️ <recommended answer>
   ```
4. Stop calling tools and await user answers for each round.
5. Continue until the design tree is resolved and the frontier is exhausted.

*Transition:* Announce completion of grilling: *"Requirements locked. Synthesizing specification (Phase 2: to-spec)..."*

---

### Phase 2: Specification (`to-spec`)
**Goal:** Synthesize everything learned into a concrete specification document without interviewing the user further.

1. **Explore the codebase**:
   - Inspect existing architectural patterns, domain vocabulary, and existing seams.
   - Prefer existing integration seams over inventing new ones. Aim for the highest seam possible.
2. **Draft the Spec Artifact**:
   - **Problem Statement**: What problem is being solved from user perspective.
   - **High-Level Solution**: Architecture and components touched.
   - **Testing Seams**: Exactly where and how the feature will be tested.
   - **Non-Goals & Edge Cases**: Explicitly out-of-scope items and handled error cases.
3. Present the spec to the user for a quick alignment check.

*Transition:* Once the spec is reviewed, proceed immediately to Phase 3.

---

### Phase 3: Ticket Decomposition (`to-tickets`)
**Goal:** Break the spec into dependency-ordered vertical slices.

1. **Define Tracer Bullets**:
   - Each ticket must be an end-to-end vertical slice (not horizontal layer splits like "all DB", then "all UI").
   - Prefer prefactoring tickets first (*"Make the change easy, then make the easy change"*).
2. **Declare Blocking Edges**:
   - Format each ticket with clear prerequisites:
     - `Ticket #1: [Prefactor / Setup]` (Blocked by: None)
     - `Ticket #2: [Core Seam & Behavior]` (Blocked by: Ticket #1)
     - `Ticket #3: [UI / Client Integration]` (Blocked by: Ticket #2)
3. Present the ticket list and execution order.

*Transition:* Confirm the ticket breakdown and proceed to Phase 4.

---

### Phase 4: Implementation (`implement`)
**Goal:** Build and verify each ticket systematically.

1. Record baseline git commit hash (`BASE_COMMIT=$(git rev-parse HEAD)`).
2. Work through tickets strictly in topological dependency order.
3. Follow verification loops:
   - Write tests first at pre-agreed seams (`tdd`).
   - Run linter & typechecker regularly (`npm run typecheck` or equivalent).
   - Run targeted test suites after edits.
4. Create atomic, meaningful git commits per ticket or logical milestone.

*Transition:* Once all tickets are implemented and all local tests pass, announce transition to Phase 5.

---

### Phase 5: Code Review (`code-review`)
**Goal:** Perform an objective, two-axis post-implementation review.

1. **Compare against baseline**:
   - Diff command: `git diff $BASE_COMMIT...HEAD`
   - Log: `git log $BASE_COMMIT..HEAD --oneline`
2. **Execute Two-Axis Review**:
   - **Axis A (Standards)**: Does the new code strictly follow repository conventions, framework idioms, typing rules, and lint standards?
   - **Axis B (Spec)**: Does the implementation satisfy all requirements, edge cases, and acceptance criteria from Phase 2 without scope creep?
3. Report findings side-by-side:
   - Critical blockers (must fix).
   - Improvements / nitpicks.
   - Verification summary.
4. If issues are found, apply immediate fixes and re-verify.

---

## Invocation Rules

- When the user runs `/ship <feature-description>`, immediately kick off **Phase 1 (Grilling)**.
- Do NOT jump straight to writing code.
- If the user provides a complete, unambiguous spec or PR description upfront, confirm if Phase 1 can be compressed into a single verification round before proceeding to Phase 2.
