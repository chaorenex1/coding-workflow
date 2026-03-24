---
description: Quick feature implementation with analysis, user approval, implementation, testing, and code cleanup
argument-hint: [feature-name|feature-description]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, TaskOutput, Skill, AskUserQuestion
---

# Quick Feature Implementation

Streamlined feature implementation workflow with analysis preview and user approval gate.

## Context

Feature to implement: $ARGUMENTS

---

## Core Protocols

- **Language Protocol:** Use **English** when interacting with tools/models, communicate with user in their language
- **Max Test Loop:** 10 iteration (if tests fail: re-analyze → re-implement → test again, then stop)
- **User Approval:** Required before implementation phase

---

## Phase 1: Prompt Enhancement (Conditional)

**Execute only if skill `prompt-optimizer` exists**

1. Check if prompt-optimizer skill exists
2. If exists, invoke it to optimize the requirement description
3. Enhanced output serves as input for subsequent phases

```
IF skill `prompt-optimizer` EXISTS:
  → Invoke prompt-optimizer with "$ARGUMENTS"
  → Use enhanced output as new requirement
ELSE:
  → Proceed with original "$ARGUMENTS"
```

---

## Phase 2: Context Retrieval and Validation

### 2.1 File Discovery (Glob)

Discover relevant source files based on the requirement:
- For Python projects: `**/*.py`
- For TypeScript/JS projects: `src/**/*.{ts,tsx,js,jsx}`
- For frontend components: `components/**/*.{vue,jsx,tsx}`

### 2.2 Key Identifier Search (Grep)

Find key symbols related to the requirement:
- Function/class/type definitions
- Export symbols
- Interface definitions

### 2.3 Context Reading (Read)

Read discovered files for complete definitions:
- Function signatures
- Type declarations
- Interface contracts

### 2.4 Completeness Validation

**Checklist:**
1. [ ] Complete class definitions and inheritance
2. [ ] Complete function signatures
3. [ ] Variable type annotations
4. [ ] Inter-module dependencies

**Recursive Retrieval:** If context insufficient, trigger additional retrieval.

---

## Phase 3: Implementation Analysis Preview

### 3.1 Generate Analysis

Based on retrieved context, generate implementation analysis:

```
## Implementation Analysis: <feature-name>

### Requirements Overview
[Enhanced requirement description]

### Technical Approach
- File structure (new/modified files)
- Key components/functions
- Data flow design

### Implementation Steps
1. [Step 1]
2. [Step 2]
3. ...

### Risks and Mitigations
| Risk | Impact | Mitigation |

### Test Strategy
[Key test scenarios]
```

### 3.2 Preview to User

```
🔍 IMPLEMENTATION ANALYSIS PREVIEW

Feature: <feature-name>

📁 Files to Create:
- [file1]: [purpose]
- [file2]: [purpose]

📁 Files to Modify:
- [file3]: [change]

🛠️ Implementation Steps:
1. [Step 1 description]
2. [Step 2 description]
3. ...

⚠️ Risks:
- [Risk 1]: [mitigation]
- [Risk 2]: [mitigation]

🧪 Test Strategy:
- [Test scenario 1]
- [Test scenario 2]

Proceed with implementation? (yes/no/modify)
```

### 3.3 Await User Decision

- `yes/是/确认` → Proceed to Phase 4
- `no/否/取消` → Terminate workflow
- `modify/修改` → Collect feedback, update analysis, re-preview

Use `AskUserQuestion` to capture the decision and do not enter Phase 4 until decision is `yes/是/确认`.

---

## Phase 4: Implementation with Cross-Validation

### 4.1 Code Implementation

Implement code based on approved analysis:
- Follow implementation steps
- Use existing code patterns
- Apply project conventions

### 4.2 Cross-Validation

After implementation, validate:
1. Code matches analysis approach
2. All identified risks addressed
3. Implementation complete per steps

**If deviation found:**
- Document discrepancy
- Adjust implementation or analysis
- Note in implementation log

---

## Phase 5: Unit Testing

### 5.1 Write Unit Tests

Write tests for implemented code:
- Happy path tests
- Edge case tests
- Error handling tests

### 5.2 Execute Tests

```bash
# Run appropriate test command based on project
npm test / pytest / cargo test / etc.
```

### 5.3 Test Result Handling

**If tests pass:**
```
✅ ALL TESTS PASSED
- [Test suite 1]: X tests passed
- [Test suite 2]: Y tests passed
```

**If tests fail (Loop iteration 1):**
1. Record failing tests and root cause
2. Return to Phase 3: Re-analyze with test feedback
3. Re-implement based on new analysis
4. Run tests again

**If tests fail again (Max loop reached):**
```
⚠️ MAXIMUM ITERATION REACHED
- Test failures persist after re-implementation
- Current status: [describe state]
-建议: [user guidance]
```

---

## Phase 6: Code Cleanup

### 6.1 Style Unification

Apply project code style:
- Consistent indentation (2/4 spaces)
- Quote style (single/double)
- Line ending consistency
- Trailing whitespace removal

### 6.2 Documentation

- Add/update comments for complex logic
- Update function/class docstrings
- Ensure JSDoc/TypeDoc annotations present

### 6.3 Final Verification

```
✅ CODE CLEANUP COMPLETE
- Style: Applied project conventions
- Comments: Updated
- Documentation: Complete
```

---

## Output Summary

```
🎉 QUICK FEATURE COMPLETE

Feature: <feature-name>
Implementation: <date>

📁 Files Created:
- [file1] ([lines] lines)
📁 Files Modified:
- [file2] ([lines] lines)

🧪 Tests: [X] passed, [Y] failed
📊 Test Loop Iterations: [N] (max 1)

✅ Feature implemented and verified
```

---

## Notes

1. **User Approval Gate:** Phase 3 requires user confirmation before implementation
2. **Test Loop Limit:** Maximum 1 re-analyze → re-implement cycle if tests fail
3. **Code Cleanup:** Final phase ensures consistent style and documentation
4. **Cross-Validation:** Implementation verified against analysis before testing
