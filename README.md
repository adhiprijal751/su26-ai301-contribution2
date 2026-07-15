# Contribution [228]: [Cleanup] Extract duplicated message-merge logic in syncFromGateway

**Contribution Number:** 2
**Student:** Adhip Rijal
**Issue:** (https://github.com/clawwork-ai/ClawWork/issues/228)
**Status:** [**Phase I** / Phase II / Phase III / Phase IV] [In Progress / **Complete**]

---

## Why I Chose This Issue

I picked this issue because it's a clean, well-scoped refactor that lets me get familiar with the session-sync service and the message/persistence data flow without needing to change any behavior — a good low-risk way to learn the codebase. Extract-function refactors like this are also good practice for spotting duplication and writing helpers with clear, minimal parameter surfaces, which is a skill I want to sharpen.I also like that the task has a clear definition of done (pnpm check passing, both branches calling the same helper), so I can validate my own work objectively rather than guessing whether I've done "enough."
---

## Understanding the Issue

### Problem Description

syncFromGateway has two branches, one for when local data already exists (hasLocalData) and one for when it doesn't (!hasLocalData). Both branches independently implement almost the same ~50 lines of logic: mapping collapsedMessages into Message[] objects (assigning sessionKey/agentId), loading them into the message store via bulkLoad, and persisting each one through deps.persistence.persistMessage(). Because this logic is copy-pasted rather than shared, any future fix or behavior change to message mapping/persistence has to be applied in two places — and it's easy to update one branch and forget the other, letting the branches silently drift out of sync.

### Expected Behavior

There should be a single, shared implementation of the "map messages → bulkLoad → persist" logic, extracted into a local helper function (e.g. loadAndPersistMessages). Both the hasLocalData and !hasLocalData branches should call this same helper, passing in only the parameters that actually differ between the two cases. Behavior of syncFromGateway should be unchanged from the outside — this is a pure refactor, not a functional fix.

### Current Behavior

The mapping, bulkLoad, and persistMessage logic is duplicated almost verbatim in both branches of the if (hasLocalData) conditional. This works correctly today, but it's fragile: the duplication makes the function longer and harder to read, and increases the risk that the two branches diverge over time as one gets patched without the other.

### Affected Components

- packages/core/src/services/session-sync.ts — specifically the syncFromGateway function (~lines 280–330)
- Indirectly touches: the messageStore (bulkLoad), and deps.persistence.persistMessage, since the new helper will call into both
- No changes expected to public APIs, message store internals, or persistence layer — this is confined to internal control flow within syncFromGateway

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
