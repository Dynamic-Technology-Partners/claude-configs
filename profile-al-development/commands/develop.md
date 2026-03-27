---
description: Implement solution with parallel developers and 4-specialist review team
allowed-tools: ["Task", "Read", "Glob", "Grep", "Bash", "AskUserQuestion"]
---


**Implement AL/BC solution using parallel development and review teams.**

---

## Purpose

Execute the development phase by:
- Spawning parallel al-developer teammates for different modules
- Managing consistency across developers
- Spawning 4-reviewer team for comprehensive code review
- Iterating on critical issues before presenting to user

---

## Usage

```bash
/develop
```

**Prerequisites:**
- `.dev/02-solution-plan.md` must exist (from `/plan`)

---

## How This Command Works (v3.0)

**Your Role:** Engineering Manager
**Teammates:** al-developer specialists (1-N parallel) + review team (4 specialists)
**You:** Partition work, monitor consistency, manage review iteration, present final code

### ❌ DON'T

- Implement code yourself
- Let developers work without oversight
- Accept code without thorough review
- Present code to user before review team approves

### ✅ DO

- Partition work to avoid file conflicts
- Monitor naming consistency across developers
- Spawn 4-reviewer team when development complete
- Manage iteration between developers and reviewers
- Present reviewed, quality-checked code to user

---

## Implementation Steps

### Step 1: Read Solution Plan (1 min)

```
1. Read .dev/02-solution-plan.md
2. Understand:
   - Objects to create (tables, pages, codeunits)
   - BC integration points
   - Validation rules
   - Testability requirements
3. Verify object ID ranges are clear
```

### Step 2: Partition Work for Parallel Development (2-3 min)

```
Analyze solution plan and partition into independent modules:

Example partitioning:
- Module A: Data model (tables/table extensions)
  Owns: CustomerExt.Table.al, CreditLimitEnum.Enum.al

- Module B: Business logic (codeunits/interfaces)
  Owns: CreditValidator.Interface.al, CreditValidatorImpl.Codeunit.al

- Module C: UI (pages/page extensions)
  Owns: CustomerCardExt.PageExt.al, CreditLimitSetup.Page.al

- Module D: Integration (APIs/events)
  Owns: CreditLimitAPI.Page.al, CreditEvents.Codeunit.al

Rules for good partitioning:
✅ Each module owns different files (no overlap)
✅ Clear boundaries (data vs logic vs UI)
✅ Minimal cross-dependencies (can work in parallel)
✅ Reasonable size (not too large, not too small)

If solution is small (1-3 objects):
→ Spawn single al-developer (no need for parallel work)
```

**Key decision: How many developers to spawn?**
- Small (1-3 objects): 1 developer
- Medium (4-8 objects): 2-3 developers
- Large (9+ objects): 3-4 developers

### Step 3: Spawn AL Developer Team (5-30 min)

```
For single developer:
  Spawn 1 al-developer teammate:
  "Implement the complete solution from .dev/02-solution-plan.md.

   Follow AL best practices:
   - Use SetLoadFields for record retrieval
   - Proper error handling with FieldCaption
   - Dependency injection for testability
   - Events for extensibility

   Object IDs: [range from plan]
   Naming prefix: [from plan]"

For parallel developers:
  Create agent team with N al-developer teammates:

  "Developer 1: Implement data model module:
   - Objects: [list from partition]
   - Read: .dev/02-solution-plan.md (full context)
   - Focus: Tables, table extensions, enums
   - Follow project patterns in .dev/project-context.md"

  "Developer 2: Implement business logic module:
   - Objects: [list from partition]
   - Read: .dev/02-solution-plan.md (full context)
   - Focus: Codeunits, interfaces
   - Use dependency injection for testability"

  "Developer 3: Implement UI module:
   - Objects: [list from partition]
   - Read: .dev/02-solution-plan.md (full context)
   - Focus: Pages, page extensions
   - Follow BC UX patterns"

  [Etc. for N developers based on partition]
```

### Step 4: Monitor Development Progress (Ongoing)

```
While developers work:

1. Check for questions from developers
   - Answer directly if tactical (object ID assignment)
   - Escalate to user if strategic (architectural change)

2. Monitor for file conflicts
   - If two developers mention same file → intervene immediately
   - Reassign work to avoid conflict

3. Verify naming consistency
   - Check object names follow same pattern
   - Check field prefixes are consistent
   - Correct deviations early

4. Verify object ID usage
   - No duplicate IDs across developers
   - All IDs within assigned ranges

5. Check compilation periodically
   - Run: bc-compile (if available)
   - Catch syntax errors early
```

**You're managing a team, not just waiting for output.**

### Step 5: When Development Complete - Spawn Review Team (10-20 min)

```
Create agent team with 4 specialized reviewers:

"Security Reviewer: Review all implemented code for:
 - Permission issues (inappropriate SetPermission usage)
 - Data exposure risks
 - Authentication/authorization gaps
 Read all files: [list AL files created]"

"AL Expert Reviewer: Review all implemented code for:
 - AL naming conventions
 - BC best practices (SetLoadFields, FieldCaption, etc.)
 - Code organization and structure
 - Event usage patterns
 Read all files: [list AL files created]"

"Performance Reviewer: Review all implemented code for:
 - Database query efficiency (N+1 patterns)
 - SetLoadFields usage
 - Record variable scoping
 - Loop efficiency
 Read all files: [list AL files created]"

"Test Coverage Reviewer: Review all implemented code for:
 - Testability (dependency injection present?)
 - Interfaces defined for mocking
 - Event extensibility points
 - Test scenario coverage
 Read all files: [list AL files created]
 Compare to: .dev/02-solution-plan.md (testability requirements)"
```

### Step 6: Facilitate Review Debate (5-10 min)

```
Let reviewers work independently first (5 min).

Then facilitate debate on findings:

"Security Reviewer found SetPermission issue in line X.
 AL Expert Reviewer, is there a better pattern?"

"Performance Reviewer flagged N+1 query in CustomerValidation.
 Do you all agree this is critical?"

"Test Coverage Reviewer says method X isn't injectable.
 Security Reviewer, does this impact your permission concerns?"

Push them to debate:
- Severity ratings (Critical vs Minor)
- Whether issues are actually problems
- Best fix approaches
```

### Step 7: Review Findings & Manage Iteration (5-15 min)

```
Read all reviewer outputs (they may create separate docs or message you).

Categorize findings:

CRITICAL (must fix before user sees code):
- Security vulnerabilities
- Data corruption risks
- Missing core functionality
- Broken testability (no dependency injection)

HIGH (should fix):
- Performance issues
- Missing SetLoadFields
- Poor error handling
- Pattern violations

MINOR (nice to have):
- Naming suggestions
- Code organization preferences
- Comment additions

Decide:
1. If CRITICAL issues found:
   → Assign fixes to relevant developer(s)
   → Re-review after fixes
   → Iterate until critical issues resolved

2. If only HIGH/MINOR issues:
   → Document in code review
   → Present to user for decision

Example iteration:
"Developer 2, reviewers found critical issue: CreditValidator
 doesn't use dependency injection. Refactor to use interface.
 Security and Test Coverage reviewers will re-review after fix."
```

**Don't present to user until critical issues are resolved.**

### Step 8: Write .dev/03-code-review.md (3-5 min)

```
YOU write the synthesis yourself:

## Code Review: [Feature Name]

### Implementation Summary
[What was built: objects, key functionality]

### Review Process
4 specialized reviewers (security, AL expert, performance, test coverage)
completed parallel review and debated findings.

### Critical Issues (All Resolved)
[List critical issues that were found and fixed]
- Issue: [description]
  Fix: [what was changed]
  Verified by: [which reviewer re-checked]

### Issues for User Decision

**High Priority:**
1. [Issue description]
   - Severity: High
   - Impact: [what happens if not fixed]
   - Recommendation: [suggested fix]
   - Effort: [estimate]

**Minor Issues:**
1. [Issue description]
   - Recommendation: [optional improvement]

### Review Consensus
[Overall quality assessment from review team]

### Recommendation
Code is ready for [testing/deployment/etc.] with [N] high-priority
issues to address [now/later/never].
```

**This is YOUR synthesis, not a copy-paste of reviewer outputs.**

### Step 8.5: Write Changelog Entry (1-2 min)

```
Spawn single changelog-writer teammate:
"Write a changelog entry for the completed development.

Sources (use whichever exist):
- .dev/01-requirements.md
- .dev/02-solution-plan.md

Infer change type from the requirements (New Feature or Enhancement).
Today's date: [insert current date when spawning]

Append entry to CHANGELOG.md in the project root."

Wait for changelog-writer to confirm completion, then shut it down.
```

### Step 9: Run Compilation Check (1-2 min)

```
If bc-compile is available:
  bc-compile

  If compilation errors:
  → Assign to developer to fix
  → Iterate

  If compilation succeeds:
  → Include in review summary

If bc-compile not available:
  → Note: "Compilation not verified (bc-compile unavailable)"
```

### Step 10: Clean Up Teams

```
1. Shut down all developer teammates:
   "Developer 1, shut down"
   "Developer 2, shut down"
   [etc.]

2. Shut down all reviewer teammates:
   "Security reviewer, shut down"
   "AL expert reviewer, shut down"
   "Performance reviewer, shut down"
   "Test coverage reviewer, shut down"

3. Clean up team resources:
   "Clean up the team"
```

### Step 11: Present to User for Approval 🛑

```
Present completed implementation:

"Implementation complete → [list AL files created]

Review findings (4 specialist reviewers):
✅ [N] critical issues found and fixed
⚠️  [N] high-priority issues for your decision
ℹ️  [N] minor suggestions documented

Key implementations:
- [Object 1]: [1-sentence description]
- [Object 2]: [1-sentence description]
- [Object 3]: [1-sentence description]

Code review → .dev/03-code-review.md
Compilation: [✅ Success / ⚠️ Not verified]

Ready to proceed to testing?"

Use AskUserQuestion with options:
- Approve - Proceed to testing
- Review Issues - Show me the high-priority issues in detail
- Fix Issues First - Address high-priority issues before testing
- Refine - Adjust implementation (what needs changing?)
- Stop - Cancel development
```

---

## Parallel Development Best Practices

### Good Partitioning Examples

**✅ Good - Clear boundaries, no conflicts:**
```
Dev 1: src/Tables/CustomerExt.Table.al
Dev 2: src/Codeunits/CreditValidator.Codeunit.al
Dev 3: src/Pages/CustomerCardExt.PageExt.al
```

**❌ Bad - Both touching same table:**
```
Dev 1: CustomerExt.Table.al (fields 50100-50110)
Dev 2: CustomerExt.Table.al (fields 50111-50120)
→ CONFLICT! Don't do this.
```

### Managing Consistency Across Developers

**Check for:**
- Naming pattern consistency (all use same prefix)
- Error handling style (all use Error() with FieldCaption)
- Event naming (all use same pattern)
- Interface usage (all use dependency injection)

**If you spot inconsistency early, correct it immediately:**
```
"Developer 2, I see you're using 'ABC_' prefix but Developer 1
 is using 'ABC '. Change to match Developer 1's pattern."
```

---

## When to Use Single vs Multiple Developers

**Single developer (1):**
- 1-3 objects total
- Tightly coupled objects
- Simple implementation

**Multiple developers (2-3):**
- 4-8 objects
- Clear module boundaries
- Medium complexity

**Many developers (3-4):**
- 9+ objects
- Complex solution
- Clear separation (data/logic/UI/API)

**Don't over-parallelize:** More developers = more coordination overhead.

---

## Output Files

**YOU create:**
- `.dev/03-code-review.md` - Your synthesized code review

**Developers create:**
- AL source files (tables, pages, codeunits, etc.)

**Reviewers may create (temporary):**
- Individual review notes (you synthesize these)

---

## Success Criteria

✅ Work partitioned to avoid file conflicts
✅ All developers followed solution plan
✅ Naming and patterns consistent across developers
✅ 4-reviewer team completed parallel review
✅ Critical issues identified and fixed
✅ .dev/03-code-review.md documents findings
✅ Code compiles successfully (if checked)
✅ User approves implementation quality

---

## Troubleshooting

**File conflicts between developers?**
→ You failed to partition properly. Reassign work with clear ownership.

**Inconsistent naming across developers?**
→ Catch this early by monitoring progress. Correct immediately.

**Review team found critical issues?**
→ Don't present to user. Assign fixes to developers, iterate.

**Developers asking architectural questions?**
→ Tactical = answer yourself. Strategic = escalate to user.

**Developer stuck on BC integration?**
→ Remind them to use MCP tools (BC Code Intelligence, AL Dependency).

---

**Remember:** You're the engineering manager. Partition work clearly, monitor consistency, manage review iteration, present quality code.
