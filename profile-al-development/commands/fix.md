---
description: Lightweight bug fix workflow without approval gates (fast iteration)
allowed-tools: ["Task", "Read", "Glob", "Grep", "Bash"]
---


**Lightweight bug fix workflow without formal approval gates.**

---

## Purpose

Quick iteration for bug fixes and small changes:
- Fast analysis (1-2 min)
- Minimal planning for non-trivial fixes
- Direct implementation
- No formal approval gates (faster flow)

---

## Usage

```bash
/fix "Customer validation not working for negative credit limits"
```

---

## How This Command Works (v3.0)

**Your Role:** Engineering Manager (but streamlined)
**Teammates:** Usually 1 al-developer, sometimes 1 solution-architect for complex fixes
**You:** Quick analysis, delegate implementation, verify fix, present

### ❌ DON'T

- Implement the fix yourself
- Skip all planning (for non-trivial fixes)
- Run this for new features (use `/plan` → `/develop` instead)

### ✅ DO

- Analyze complexity first (trivial vs non-trivial)
- Spawn architect for quick plan if needed (5 min max)
- Spawn single developer for implementation
- Verify fix compiles
- Present fix directly (no formal approval gate)

---

## Implementation Steps

### Step 1: Quick Analysis (1-2 min)

```
Read user's fix request and classify complexity:

TRIVIAL (90% of fixes):
- Typo or simple logic error
- Missing validation
- Wrong field reference
- Clear, obvious fix
- Single file change

Example: "Fix typo in error message"
Example: "Add missing null check"
Example: "Change >= to > in validation"

NON-TRIVIAL (10% of fixes):
- Root cause unclear
- Multiple files affected
- BC integration issue
- Architectural change needed
- Requires understanding complex flow

Example: "Posting fails intermittently"
Example: "Event subscriber not triggering"
Example: "Performance degradation after upgrade"
```

### Step 2a: Trivial Fix (2-5 min)

```
For trivial fixes:

1. Identify the file and issue
   - Use Grep/Read to locate the problem
   - Verify you understand the issue

2. Spawn single al-developer:
   "Fix [specific issue] in [file path].

    Issue: [description]
    Expected fix: [what needs to change]

    Verify the fix compiles.
    Keep it minimal - only change what's necessary."

3. Developer implements fix

4. Run compilation check (if bc-compile available):
   bc-compile

5. Write changelog entry:
   Spawn single changelog-writer teammate:
   "Write a changelog entry for this bug fix.
    Description: [user's original fix request]
    Change type: Bug Fix
    Today's date: [insert current date]
    Append to CHANGELOG.md in the project root."
   Wait for confirmation, shut down changelog-writer.

6. Present fix to user:
   "Fix complete → [file path]

    Changed: [1-line description]
    [Show code diff if small]

    Compilation: [✅ Success / Not verified]

    Ready to test?"

7. Clean up (shut down developer)
```

**No approval gate - present fix directly.**

### Step 2b: Non-Trivial Fix (10-20 min)

```
For non-trivial fixes:

1. Spawn solution-architect for quick analysis:
   "Analyze this issue and provide quick fix approach:

    Issue: [user's description]
    Context: [relevant files/objects]

    Provide:
    1. Root cause hypothesis (2-3 sentences)
    2. Recommended fix approach (bullet points)
    3. Files that need changes
    4. Risks/side effects to watch for

    Keep it concise - 5 min analysis, not full solution plan."

2. Review architect's analysis yourself:
   - Does root cause make sense?
   - Is fix approach sound?
   - Are there risks?

3. If approach unclear, refine:
   "Your hypothesis about [X] doesn't account for [Y].
    Re-analyze considering [constraint]."

4. Once you approve the approach, spawn al-developer:
   "Implement fix based on this approach:

    Root cause: [from architect]
    Fix approach: [from architect]
    Files to change: [from architect]

    Follow the recommended approach.
    Verify compilation after changes."

5. Developer implements fix

6. Run compilation check:
   bc-compile

7. Write changelog entry:
   Spawn single changelog-writer teammate:
   "Write a changelog entry for this bug fix.
    Description: [user's original fix request + root cause from architect]
    Change type: Bug Fix
    Today's date: [insert current date]
    Append to CHANGELOG.md in the project root."
   Wait for confirmation, shut down changelog-writer.

8. Present fix to user:
   "Fix complete → [files changed]

    Root cause: [brief explanation]
    Fix approach: [1-2 sentences]

    Changed files:
    - [file 1]: [change description]
    - [file 2]: [change description]

    Compilation: [✅ Success / Not verified]

    Risks to watch: [from architect analysis]

    Ready to test?"

9. Clean up (shut down architect, developer)
```

**Still no approval gate, but more context provided.**

---

## Decision Tree

```
User: "/fix [issue]"
    ↓
You: Analyze complexity
    ↓
    ├─→ TRIVIAL (simple, obvious)
    │   ├─→ Spawn 1 al-developer
    │   ├─→ Fix implemented
    │   ├─→ Verify compilation
    │   └─→ Present to user ✅
    │
    └─→ NON-TRIVIAL (complex, unclear)
        ├─→ Spawn solution-architect (5 min analysis)
        ├─→ Review approach yourself
        ├─→ Spawn al-developer with approach
        ├─→ Fix implemented
        ├─→ Verify compilation
        └─→ Present to user ✅
```

---

## When to Use /fix vs Other Commands

**✅ Use /fix for:**
- Bug fixes
- Small corrections
- Logic errors
- Missing validations
- Quick improvements
- Anything you'd call a "fix" not a "feature"

**❌ Don't use /fix for:**
- New features → Use `/plan` then `/develop`
- Architectural changes → Use `/plan` first
- Multiple related changes → Use `/plan` for coordination
- Anything needing formal approval gates

**Rule of thumb:** If it's fixing something broken, use `/fix`. If it's adding something new, use `/plan`.

---

## Fast Iteration Philosophy

This command intentionally **skips approval gates** for fast iteration:

**Traditional workflow:**
```
Fix → Code Review → User Approval → Testing
(Slow but thorough)
```

**/fix workflow:**
```
Fix → Compilation Check → Present to User → User Tests
(Fast but requires user to verify)
```

**Trade-off accepted:**
- ✅ Faster feedback loop
- ✅ Less overhead for small changes
- ❌ User must verify fix works (testing phase)
- ❌ Less formal review process

**This is intentional** - fixes need speed, features need thoroughness.

---

## Compilation Verification

Always try to verify compilation:

```bash
# If bc-compile available
bc-compile

# Report result
✅ Compilation successful
❌ Compilation failed: [error]
⚠️  Compilation not verified (bc-compile unavailable)
```

**If compilation fails:**
- Have developer fix errors
- Re-compile
- Don't present to user until it compiles

---

## Examples

### Example 1: Trivial Fix

```
User: "/fix Customer validation allows negative credit limits"

You (analysis):
"Trivial - missing validation check in Customer table extension"

You (to developer):
"Add validation in CustomerExt OnValidate trigger for Credit Limit field.
 Error if value < 0."

Developer: [Implements]

You (to user):
"Fix complete → CustomerExt.Table.al

 Added validation: Credit Limit cannot be negative
 Compilation: ✅ Success

 Ready to test?"
```

**Time: 3-4 minutes**

### Example 2: Non-Trivial Fix

```
User: "/fix Sales posting fails when credit limit is exactly 0"

You (analysis):
"Non-trivial - need to understand posting flow and where check happens"

You (spawn architect):
"Analyze why posting fails when credit limit is exactly 0"

Architect:
"Root cause: CreditValidator uses > instead of >= for comparison.
 Fix: Change comparison logic in ValidateCredit method.
 Risk: Ensure 0 is treated as 'no limit' if that's business rule."

You (review):
"Approach makes sense. Clarify: is 0 'no limit' or 'zero limit'?
 [Ask user or check existing code patterns]"

[User clarifies or you determine from code]

You (to developer):
"Fix comparison in CreditValidator.Codeunit.al ValidateCredit method.
 Change > to >= and document that 0 means [no limit/zero limit]."

Developer: [Implements]

You (to user):
"Fix complete → CreditValidator.Codeunit.al

 Root cause: Comparison logic excluded exactly 0
 Fix: Changed > to >= in ValidateCredit method
 Documented: 0 is treated as [no limit/zero limit]

 Compilation: ✅ Success
 Risk: Watch for edge cases where 0 limit is set intentionally

 Ready to test?"
```

**Time: 12-15 minutes**

---

## Success Criteria

✅ Complexity classified correctly (trivial vs non-trivial)
✅ Developer spawned with clear instructions
✅ Architect spawned only when needed (not for trivial fixes)
✅ Fix compiles successfully
✅ Root cause understood (for non-trivial fixes)
✅ Risks identified (for non-trivial fixes)
✅ Presented to user quickly (no approval gate delays)

---

## Troubleshooting

**Can't determine if trivial or non-trivial?**
→ Default to non-trivial (spawn architect for 5 min analysis)

**Architect's analysis is wrong?**
→ Challenge it yourself, don't blindly accept

**Developer's fix doesn't compile?**
→ Have them fix errors, re-compile, iterate

**Fix seems to need multiple approaches?**
→ This might be a feature, not a fix. Suggest `/plan` instead.

**Unclear if 0 vs null vs empty means "no limit"?**
→ Grep existing code for patterns, or escalate to user

---

**Remember:** /fix is for speed. Analyze quickly, delegate clearly, verify compilation, present directly.
