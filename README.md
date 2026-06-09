# su26-ai301-contribution
# Contribution [#]: [Issue Title]

**Contribution Number:** 1 

**Student:** Saleeq Adnan Syed

**Issue:** https://github.com/backstage/community-plugins/issues/9188 

**Status:** Phase I — In Progress

---

## Why I Chose This Issue

 I chose issue #9188 in the backstage/community-plugins repository because it is a well-scoped TypeScript feature addition with clear acceptance criteria. The azure-devops readme plugin currently forces users to manually define the `dev.azure.com/readme-path` annotation for every entity in a monorepo, even when a README.md already exists next to the catalog-info.yaml file.

This matches my TypeScript experience and my goal of contributing to a large, production-grade open source project. "Done" is clearly defined: the annotation becomes optional when README.md is co-located with catalog-info.yaml. The original requester also expressed willingness to collaborate, and the maintainer added `help wanted` last week — signaling active interest in a PR.

---

## Understanding the Issue

### Problem Description

The azure-devops-readme plugin requires users to explicitly set `dev.azure.com/readme-path` for every entity. In monorepos where each component has its own README.md next to catalog-info.yaml, this creates unnecessary repetitive configuration.

### Expected Behavior

The plugin should auto-detect README.md at the same directory level as catalog-info.yaml and use it as a fallback when no explicit annotation is provided.

### Current Behavior

Without the `dev.azure.com/readme-path` annotation, the plugin does not display any README — even when one exists right next to the catalog-info.yaml.

### Affected Components

The azure-devops workspace inside backstage/community-plugins, specifically the readme plugin's path resolution logic.

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
