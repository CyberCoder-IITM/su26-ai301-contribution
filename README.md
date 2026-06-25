# su26-ai301-contribution
# Contribution #1: Auto-detect README.md in azure-devops-readme plugin

**Contribution Number:** 1 

**Student:** Saleeq Adnan Syed

**Issue:** https://github.com/backstage/community-plugins/issues/9188 

**Status:** Phase III — Complete | Awaiting Maintainer Review

**PR:** https://github.com/backstage/community-plugins/pull/9703

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

The azure-devops workspace inside backstage/community-plugins, specifically:
- `plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.ts`
- `plugins/azure-devops/src/hooks/useReadme.ts`
---

## Reproduction Process

### Environment Setup

- OS: Windows 11 / MINGW64 (Git Bash)
- Node v24.15.0 (supported: `"node": "22 || 24"` per root package.json engines field)
- Yarn 4.14.1 via local binary (`node .yarn/releases/yarn-4.14.1.cjs`)
- Corepack unavailable due to Windows permissions — used local Yarn binary directly
- Workspace install: `yarn install` run at both repo root and `workspaces/azure-devops`
- Commits: used `HUSKY=0` flag due to Corepack/PATH issue with Husky pre-commit hook
- Setup time: ~90 minutes including dependency installs

### Steps to Reproduce

This is a feature request, not a bug. "Reproduction" means demonstrating the
behavioral gap through code tracing:
 
1. Clone fork and navigate to `workspaces/azure-devops`
2. Open `plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.ts`
3. Locate lines 41–42 where `readmePath` is assigned:
```typescript
const readmePath =
  entity.metadata.annotations?.[AZURE_DEVOPS_README_ANNOTATION];
```
4. Observe: value comes **exclusively** from the annotation — no fallback logic exists
5. Run: `grep -rn "source-location" --include="*.ts" .` from `workspaces/azure-devops`
6. **Result: zero matches** — `backstage.io/source-location` is never read anywhere in the workspace
7. **Expected:** When annotation is absent, plugin derives README.md path from `backstage.io/source-location`
8. **Actual:** `readmePath = undefined` — no fallback exists anywhere in the workspace

### Code Trace Evidence

| File | Location | Finding |
|------|----------|---------|
| `getAnnotationValuesFromEntity.ts` | Lines 41–42 | `readmePath` only reads from annotation, no fallback |
| `useReadme.ts` | Line 49 | `path: readmePath` passes undefined straight to API |
| `constants.ts` | Entire file | No `source-location` constant exists |
| `grep source-location` | Entire workspace | **Zero results** — feature completely absent |


### Reproduction Evidence

- **Commit 1 (scaffold):** https://github.com/CyberCoder-IITM/community-plugins/commit/2cd14f4ac
- **Commit 2 (changeset):** https://github.com/CyberCoder-IITM/community-plugins/commit/7a82de55c
- **Branch:** https://github.com/CyberCoder-IITM/community-plugins/tree/feat/azure-devops-readme-auto-detect-9188
- **Test run:** 21/21 existing tests passing with scaffold in place — zero regressions

- **Commit showing reproduction:** https://github.com/CyberCoder-IITM/community-plugins/commit/2cd14f4ac
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

The azure-devops-readme plugin reads `dev.azure.com/readme-path` from entity
annotations to fetch a README from Azure DevOps. If the annotation is absent, no
README is displayed — even when a `README.md` exists alongside `catalog-info.yaml`.
In monorepos, this forces teams to annotate every entity manually, which is the
problem reported in issue #9188.

**Match:** [What similar patterns/solutions exist in the codebase?]
`backstage.io/source-location` is a standard Backstage annotation set automatically
during catalog ingestion. Its value is a URL pointing to the catalog-info.yaml
location, e.g.:
```
url:https://dev.azure.com/org/project/_git/repo?path=%2Fservices%2Fapi%2Fcatalog-info.yaml
```
Replacing `catalog-info.yaml` with `README.md` in this path gives the auto-detected
readme location — no extra API calls needed. `ANNOTATION_SOURCE_LOCATION` is already
exported from `@backstage/catalog-model` which is an existing dependency.

**Plan:** [Step-by-step implementation plan]
1. ✅ Import `ANNOTATION_SOURCE_LOCATION` from `@backstage/catalog-model`
2. ✅ Scaffold fallback IIFE structure in `getAnnotationValuesFromEntity.ts`
3. ⏳ Strip the `url:` prefix from the source-location value
4. ⏳ Parse the URL and extract the `path` query parameter
5. ⏳ Replace `catalog-info.yaml` with `README.md` in the extracted path
6. ⏳ Return derived path as fallback `readmePath`
7. ⏳ If source-location absent or unparseable → return `undefined` gracefully (no crash)
8. ⏳ Add 4 unit tests covering all cases


**Files to modify:**
- `plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.ts` ✅ Scaffolded
- `plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.test.ts` ⏳ Phase III

**Implement:** [Link to your branch/commits as you work]
https://github.com/CyberCoder-IITM/community-plugins/tree/feat/azure-devops-readme-auto-detect-9188

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

- ✅ Read `CONTRIBUTING.md` — changeset required, DCO sign-off required, conventional commits required
- ✅ Changeset filed (`patch` bump for `@backstage-community/plugin-azure-devops-common`)
- ✅ All commits signed with `-s` flag (DCO compliant)
- ✅ Conventional commit format used (`feat(azure-devops):`, `chore:`)
- ⏳ Will run `yarn build:api-reports` before opening PR

**Evaluate:** [How will you verify it works?]
- Test 1: annotation present → uses annotation value (regression: existing behavior unchanged)
- Test 2: annotation absent + valid Azure source-location → derives `README.md` path correctly
- Test 3: annotation absent + source-location missing → returns `undefined` gracefully
- Test 4: annotation absent + non-Azure URL format → returns `undefined` gracefully
- All 21 existing tests confirmed passing ✅

---

## Testing Strategy

### Unit Tests

- [ ] annotation present → uses annotation path (regression test — existing behavior)
- [ ] annotation absent + valid Azure source-location → auto-detects README.md path
- [ ] annotation absent + source-location annotation missing → returns undefined gracefully
- [ ] annotation absent + non-Azure source-location URL → returns undefined gracefully

### Integration Tests

- [ ] Plugin renders README when source-location points to valid Azure DevOps path
- [ ] Plugin renders nothing (no crash) when neither annotation nor source-location is present

### Manual Testing [What you tested manually and results]

- Run plugin locally against mock entity without `dev.azure.com/readme-path` annotation
- Confirm README renders when `backstage.io/source-location` points to a valid Azure path
- Confirm no crash when both annotations are absent

---
## Phase III — Solution Building

**Status:** ✅ Complete

**What I built:**

Completed the full implementation of README.md auto-detection for backstage/community-plugins#9188. Modified `getAnnotationValuesFromEntity()` in `plugin-azure-devops-common` to fall back to `backstage.io/source-location` when `dev.azure.com/readme-path` is absent. The fallback extracts the Azure DevOps `path` query parameter and resolves `README.md` in the same directory as the catalog file. Non-Azure URLs, malformed values, and absent annotations all return `undefined` gracefully with zero regressions. Expanded the test suite from 21 to 29 tests (8 new cases). Updated plugin documentation, filed changesets for both affected packages, and addressed Copilot review feedback.

**Key commits:**
- [`258569207`](https://github.com/CyberCoder-IITM/community-plugins/commit/258569207) — feat: implement readme path auto-detection from source-location
- [`13edbdea6`](https://github.com/CyberCoder-IITM/community-plugins/commit/13edbdea6) — test: add 8 new tests (29/29 passing)
- [`154cde897`](https://github.com/CyberCoder-IITM/community-plugins/commit/154cde897) — docs: document README auto-detection from source-location
- [`d94505ed6`](https://github.com/CyberCoder-IITM/community-plugins/commit/d94505ed6) — chore: add changeset for azure-devops plugin docs update
- [`fd3918c46`](https://github.com/CyberCoder-IITM/community-plugins/commit/fd3918c46) — fix: address Copilot review comments (hostname exact match + slash guard)

**Test Results:** 29/29 passing (0 regressions)

**Files modified:**
- `workspaces/azure-devops/plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.ts`
- `workspaces/azure-devops/plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.test.ts`
- `workspaces/azure-devops/plugins/azure-devops/README.md`
- `workspaces/azure-devops/.changeset/rich-comics-complain.md`
- `workspaces/azure-devops/.changeset/shy-bulldogs-grow.md`

**Maintainer Feedback Received:**
- Copilot AI flagged 2 issues: hostname check too permissive + edge case when path has no slash
- Both fixed in `fd3918c46` and confirmed with 29/29 tests still passing

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Week 2 Progress (Phase II)
 
**What I built:**
- Cloned fork, resolved Yarn 4 / Corepack permission issue on Windows
- Created branch `feat/azure-devops-readme-auto-detect-9188`
- Ran `grep`-based code trace across entire `workspaces/azure-devops`
- Identified exact feature gap: `getAnnotationValuesFromEntity.ts` lines 41–42
- Confirmed `backstage.io/source-location` never referenced anywhere in workspace (grep returns empty)
- Scaffolded fallback structure with `ANNOTATION_SOURCE_LOCATION` import and IIFE pattern
- All 21 existing tests passing with scaffold in place — zero regressions
- Filed changeset (`patch` bump) per CONTRIBUTING.md requirements
**Challenges faced:**
- Yarn 4 / Corepack requires admin permissions on Windows to enable globally — worked around using local binary
- Husky pre-commit hook couldn't find `yarn` in PATH — resolved with `HUSKY=0` environment variable
- VS Code showed false IntelliSense errors for internal package imports — confirmed via terminal compiler that these are cosmetic only

**Files modified:**
- `workspaces/azure-devops/plugins/azure-devops-common/src/utils/getAnnotationValuesFromEntity.ts`
- `workspaces/azure-devops/.changeset/rich-comics-complain.md` (generated by `yarn changeset`)
**Key commits:**
- [`2cd14f4ac`](https://github.com/CyberCoder-IITM/community-plugins/commit/2cd14f4ac) — `feat(azure-devops): scaffold readme path auto-detection fallback (#9188)`
- [`7a82de55c`](https://github.com/CyberCoder-IITM/community-plugins/commit/7a82de55c) — `chore: add changeset for readme auto-detection feature (#9188)`
---
 

### Code Changes

- **Files modified:** [List]

  
- **Key commits:** [Links to important commits]


- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** https://github.com/backstage/community-plugins/pull/9703

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

**Maintainer Feedback:**
- Copilot AI review flagged 2 issues — both fixed in fd3918c46

**Status:** Open — Awaiting human maintainer review from awanlin

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

- Navigating a large Yarn 4 monorepo (backstage/community-plugins — 8,700+ commits)
- Using `grep`-based code tracing to identify feature gaps without running the full app
- Understanding Backstage's annotation system (`backstage.io/source-location`)
- Changeset workflow for versioned open source releases
- DCO sign-off requirements for enterprise open source projects

### Challenges Overcome

[What was hard and how you solved it]

- **Yarn 4 / Corepack on Windows:** Corepack requires admin permissions to write to `C:\Program Files\nodejs`. Resolved by calling the local Yarn binary directly via `node .yarn/releases/yarn-4.14.1.cjs`
- **Husky pre-commit hook:** Hook runs in a child shell where the Yarn alias is not available. Resolved with `HUSKY=0` environment variable (official Husky bypass flag)
- **VS Code false errors:** IntelliSense showed `Cannot find module` errors for internal workspace packages. Confirmed via terminal compiler these are cosmetic — workspace install resolves them at build time

### What I'd Do Differently Next Time

[Reflection on your process]

- Run `corepack enable` as administrator during initial machine setup to avoid Husky/PATH issues
- Add `export HUSKY=0` to `~/.bashrc` permanently from the start on Windows
- Check `.nvmrc` and `engines` field before installing to avoid Node version surprises
 
---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]

- https://github.com/backstage/community-plugins/blob/main/CONTRIBUTING.md
- https://github.com/backstage/community-plugins/issues/9188
- https://backstage.io/docs/features/software-catalog/well-known-annotations
- https://github.com/atlassian/changesets/blob/main/docs/intro-to-using-changesets.md
- https://typicode.github.io/husky/how-to.html (HUSKY=0 bypass documentation)
