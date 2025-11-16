## Spec Refinement

### Purpose
Iteratively improve an existing HUSL specification based on feedback or new requirements.

### Prompt Template

```
You are a technical writer and business analyst refining a Human-Understandable Specification Language (HUSL) specification.

CONTEXT:
An existing HUSL specification needs to be improved based on feedback, new requirements, or clarifications. Your goal is to update the spec while maintaining its quality and consistency.

CURRENT SPECIFICATION:
[Paste .husl.md file]

REFINEMENT REQUEST:
[Describe what needs to change:
- New requirements to add
- Feedback to address
- Clarifications needed
- Issues to fix]

TASK:
Update the specification to address the refinement request while maintaining HUSL format and quality.

REFINEMENT GUIDELINES:

1. **Maintain consistency:**
   - Update all related sections
   - Update cross-references
   - Keep naming conventions
   - Update version history

2. **Impact analysis:**
   - Identify what else needs to change
   - Check for breaking changes
   - Update tests affected
   - Update related operations

3. **Documentation:**
   - Add clear comments explaining changes
   - Update version number appropriately (major/minor/patch)
   - Document migration notes if breaking
   - Add rationale for changes

4. **Quality:**
   - Maintain or improve clarity
   - Keep spec complete
   - Ensure testability
   - Validate business logic

OUTPUT FORMAT:

1. **First, provide an impact analysis:**

# Impact Analysis

**Change Request:**
[Summary of requested changes]

**Sections Affected:**
- [Section 1]: [What needs to change]
- [Section 2]: [What needs to change]

**Breaking Changes:**
- ✅ None
OR
- ⚠️ [Description of breaking changes]

**Version Change:**
- Current: [X.Y.Z]
- New: [X.Y.Z]
- Reason: [Major/Minor/Patch because...]

**Related Updates Needed:**
- [Update 1]
- [Update 2]

---

2. **Then, provide the updated specification:**

[Complete updated .husl.md file with changes]

---

3. **Finally, provide a change summary:**

# Change Summary

## Changes Made

### Schema Section
- Added: [Entity/Field]
- Modified: [Entity/Field] - [what changed]
- Removed: [Entity/Field] - [why]

### Operations Section
- Added: [Operation]
- Modified: [Operation] - [what changed]
- Removed: [Operation] - [why]

[Continue for all sections]

## Migration Notes

**For Implementers:**
- [What code needs to change]
- [What tests need updating]

**For API Consumers:**
- [What API changes they'll see]
- [How to migrate]

## Testing Requirements

**New Tests Needed:**
- [Test scenario 1]
- [Test scenario 2]

**Tests to Update:**
- [Test 1] - [why]

---

REFINEMENT GUIDELINES:

- Make minimal changes to address request
- Update everything that's affected
- Maintain spec quality
- Document why changes were made
- Consider backwards compatibility
- Update version appropriately

BEGIN REFINEMENT:
```
