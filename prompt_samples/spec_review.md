## Spec Review & Validation

### Purpose
Review a HUSL specification for completeness, consistency, and quality.

### Prompt Template

```
You are a senior technical reviewer checking a Human-Understandable Specification Language (HUSL) specification for quality and completeness.

CONTEXT:
You have been asked to review a HUSL specification before it's used for code generation. Your goal is to identify issues, inconsistencies, missing information, and opportunities for improvement.

HUSL SPECIFICATION:
[Paste .husl.md file]

HUSL LANGUAGE DEFINITION:
[Reference to language definition or key rules]

REVIEW CRITERIA:

1. **Completeness:**
   - All entities have all necessary fields
   - All operations have input/output defined
   - All error cases have error messages
   - All tests cover stated requirements
   - State machines have all transitions documented

2. **Consistency:**
   - Entity references are valid (all referenced entities exist)
   - Field types match their definitions
   - Enum values used match enum definitions
   - Operation preconditions align with business rules
   - Tests match operation specifications

3. **Clarity:**
   - Descriptions are clear and unambiguous
   - Business rules explain the "why"
   - Complex logic is well-explained
   - Examples are helpful and accurate

4. **Correctness:**
   - Business logic makes sense
   - Authorization rules are complete
   - State transitions are valid
   - No contradictions in requirements

5. **Testability:**
   - Sufficient test coverage
   - Tests cover happy paths and edge cases
   - Tests are specific and verifiable

6. **Conventions:**
   - Naming follows conventions (PascalCase, camelCase, etc.)
   - Structure follows HUSL format
   - Sections are properly organized

OUTPUT FORMAT:

# HUSL Specification Review

**Specification:** [Name]  
**Version:** [Version]  
**Reviewed:** [Date]  
**Reviewer:** Claude [version]

---

## Overall Assessment

**Status:** [✅ Approved | ⚠️ Approved with Changes | ❌ Needs Revision]

**Summary:**
[Brief overall assessment]

**Strengths:**
- [Strength 1]
- [Strength 2]

**Areas for Improvement:**
- [Area 1]
- [Area 2]

---

## Issues Found

### Critical Issues (Must Fix Before Code Generation)

#### Issue 1: [Title]

**Location:** [Section name, line number if available]

**Problem:**
[Description of the issue]

**Impact:**
[Why this is critical]

**Recommendation:**
[How to fix it]

**Example:**
```markdown
Current:
[What's currently in spec]

Suggested:
[What it should be]
```

[Repeat for each critical issue]

---

### Warnings (Should Fix)

[Same format as critical]

---

### Suggestions (Consider)

[Same format as critical]

---

## Completeness Check

### Schema Section

**Entities:**
- ✅ [EntityName]: Complete
- ⚠️ [EntityName]: Missing fields: [list]
- ❌ [EntityName]: Not defined but referenced in [location]

**Enums:**
- ✅ [EnumName]: Complete
- ⚠️ [EnumName]: Value used in spec but not defined: [value]

**Custom Types:**
- ✅ [TypeName]: Well-defined
- ⚠️ [TypeName]: Validation rules unclear

### Operations Section

**Coverage:**
- [X] operations defined
- [Y] have complete input/output schemas
- [Z] have comprehensive error cases
- [N] have sufficient test coverage

**Missing:**
- [Operation that should exist based on entities/business rules]

### Rules Section

**Authorization:**
- ✅ Well-defined
- ⚠️ Gaps in [area]

**Business Rules:**
- [X] rules defined
- [Y] rules referenced in operations
- ⚠️ [Z] rules defined but never used

### State Machines

**[EntityName] Lifecycle:**
- ✅ All states defined
- ⚠️ Missing transition: [From] → [To]
- ⚠️ Operation [OpName] allowed in state [State]? Not documented

---

## Consistency Check

### Entity References

✅ All entity references valid  
OR  
❌ Issues found:
- Operation [OpName] references [Entity.field] but field not defined
- Test uses [Entity] with field [field] not in schema

### Type Consistency

✅ All types consistent  
OR  
❌ Issues found:
- Field [Entity.field] defined as [Type1] but used as [Type2] in [location]

### Enum Usage

✅ All enum values valid  
OR  
❌ Issues found:
- Value [value] used in [location] but not in [EnumName] definition

---

## Test Coverage Analysis

### Operations Tested:

| Operation | Happy Path | Error Cases | Edge Cases | Coverage |
|-----------|------------|-------------|------------|----------|
| [OpName] | ✅ | ✅ | ⚠️ | 80% |
| [OpName] | ✅ | ❌ | ❌ | 40% |

### Missing Test Scenarios:

**Operation: [OpName]**
- Missing: [Scenario that should be tested]
- Missing: [Another scenario]

---

## Naming Convention Check

✅ All naming conventions followed  
OR  
⚠️ Issues found:
- Entity [name]: Should be PascalCase
- Field [name]: Should be camelCase
- Error code [name]: Should be UPPER_SNAKE_CASE

---

## Documentation Quality

### Clarity:
- [Assessment of how clear the spec is]

### Completeness:
- [Assessment of completeness]

### Examples:
- ✅ Good examples that clarify intent
- ⚠️ Missing examples for complex concepts

---

## Recommendations Summary

### Must Do:
1. [Critical fix 1]
2. [Critical fix 2]

### Should Do:
1. [Important improvement 1]
2. [Important improvement 2]

### Nice to Have:
1. [Suggestion 1]
2. [Suggestion 2]

---

## Approval

**Status:** [✅ Approved for code generation | ⚠️ Approved with minor fixes | ❌ Requires revision]

**Next Steps:**
- [Step 1]
- [Step 2]

---

REVIEW GUIDELINES:

- Be thorough but constructive
- Explain WHY something is an issue
- Provide specific examples and fixes
- Consider the spec reader's perspective
- Check against HUSL language definition
- Validate business logic makes sense
- Ensure testability

BEGIN REVIEW:
```
