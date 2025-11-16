## Stack Upgrade Analysis

### Purpose
Analyze the impact of upgrading technology stack versions and identify improvement opportunities.

### Prompt Template

```
You are a senior software architect analyzing a technology stack upgrade.

CONTEXT:
A service is considering upgrading its technology stack. You need to analyze the impact, identify new features that could improve the codebase, assess risks, and create a migration plan.

INPUTS PROVIDED:

Current Stack (stack.yml):
[Paste current stack.yml]

Proposed Stack (stack.yml):
[Paste proposed stack.yml]

HUSL Specification:
[Paste .husl.md file]

Current Generated Code (optional):
[Paste key files or reference codebase]

TASK:
Generate a comprehensive Stack Upgrade Report analyzing the impact and opportunities.

ANALYSIS REQUIREMENTS:

1. **Research new features:**
   - Review release notes for all version upgrades
   - Identify language features added since current version
   - Identify framework features added since current version
   - Note performance improvements
   - Note security fixes

2. **Map to current code:**
   - Find specific examples in the spec/code that could benefit
   - Provide before/after code examples
   - Estimate impact (% improvement, lines of code reduced)

3. **Assess compatibility:**
   - Check for breaking changes
   - Identify deprecated features in use
   - Note migration requirements

4. **Estimate effort:**
   - Low: Configuration only, automatic via regeneration
   - Medium: Some code changes, testing required
   - High: Significant refactoring, extensive testing

5. **Risk assessment:**
   - Identify potential issues
   - Consider rollback complexity
   - Note testing requirements

6. **Prioritize recommendations:**
   - High priority: Security fixes, easy wins, big impact
   - Medium priority: Nice improvements, moderate effort
   - Low priority: Future considerations

OUTPUT FORMAT:

# Stack Upgrade Report

**Generated:** [timestamp]  
**Analyzer:** Claude [version]

## Proposed Changes

**From:**
```yaml
[Current stack]
```

**To:**
```yaml
[Proposed stack]
```

---

## Executive Summary

**Recommendation:** [Proceed | Proceed with cautions | Delay]

**Key Benefits:**
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

**Key Risks:**
- [Risk 1]
- [Risk 2]

**Estimated Effort:** [X] days  
**Overall Risk:** [Low | Medium | High]

---

## Impact Analysis

### [Component] Upgrade: [Old] → [New]

**New Features Available:**

#### 1. Feature: [FeatureName]

**What:** [Description of the feature]

**Current Usage:** [How many places in code could use this]

**Opportunity:** [How this could improve the codebase]

**Example:**
```[language]
// Current ([old version])
[Code example from spec]

// Proposed ([new version])
[Improved code example]
```

**Impact:** [Quantified benefit]  
**Effort:** [Low | Medium | High]  
**Recommendation:** [Implement immediately | Consider | Future]

[Repeat for each feature]

**Deprecations & Removals:**
- [Any features being removed]
- [Migration path if used]

**Compatibility:**
- ✅ | ⚠️ | ❌ [Compatibility status]
- [Details]

---

## Dependency Analysis

**Updated Transitive Dependencies:**

| Dependency | Current | New | Impact |
|------------|---------|-----|--------|
| [name] | [version] | [version] | ✅ [description] |

**Security Vulnerabilities Fixed:**
- CVE-XXXX: [Description] ([severity])

---

## Recommendations

### High Priority (Implement Immediately)

**1. [Recommendation]**

**Why:** [Justification]  
**Effort:** [estimate]  
**Risk:** [Low/Medium/High]  
**Action:** [Specific steps]

[Repeat for each high priority item]

### Medium Priority (Consider for Next Sprint)

[Same format]

### Low Priority (Future Consideration)

[Same format]

---

## Migration Plan

### Phase 1: [Name] (Week X)

**Goals:**
- [Goal 1]
- [Goal 2]

**Steps:**

**Day 1:**
- [Task]
- [Task]

**Day 2:**
- [Task]
- [Task]

**Rollback Plan:**
- [How to rollback if issues]

### Phase 2: [Name] (Week X)

[Same format]

---

## Testing Strategy

### Automated Tests
- [What tests must pass]

### Manual Verification
- [What to verify manually]

### Performance Benchmarks
- Baseline: [Current performance]
- Target: [Expected performance]
- Regression threshold: [Acceptable variation]

---

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| [Risk 1] | [Low/Med/High] | [Low/Med/High] | [How to mitigate] |

**Overall Risk:** [Low | Medium | High]

**Risk Factors:**
- [Factor 1]
- [Factor 2]

---

## Cost-Benefit Analysis

**Costs:**
- [X] days engineering time
- [Other costs]

**Benefits:**
- Performance: [quantified]
- Security: [quantified]
- Maintainability: [description]
- Future-proofing: [description]

**ROI:** [High | Medium | Low]

---

## Sign-Off Checklist

Before proceeding with upgrade:

- [ ] Upgrade report reviewed by tech lead
- [ ] Spec version incremented (if spec changes needed)
- [ ] Tests pass with new stack in local environment
- [ ] Performance benchmarks completed
- [ ] Staging deployment successful
- [ ] Rollback plan documented
- [ ] Team trained on new features
- [ ] Monitoring configured

---

## Appendix: Generated Code Diff Summary

**Files Changed:** [count]  
**Lines Added:** [count]  
**Lines Removed:** [count]  
**Net Change:** [±count] lines

**Key Changes:**
- [Change 1]
- [Change 2]

**Review Priority Files:**
- [File]: [Why important to review]

---

ANALYSIS GUIDELINES:

- Research thoroughly: Check official release notes and migration guides
- Be specific: Provide actual code examples from the spec
- Quantify impact: Use numbers (% improvement, lines reduced, etc.)
- Consider the whole system: How does this affect all components?
- Be realistic: Factor in testing and validation time
- Provide escape hatches: Always have a rollback plan

BEGIN ANALYSIS:
```

### Usage Example

```
You are a senior software architect analyzing a technology stack upgrade.

[... paste full prompt ...]

Current Stack:
```yaml
language: {name: java, version: "17"}
framework: {name: spring-boot, version: "3.2.0"}
```

Proposed Stack:
```yaml
language: {name: java, version: "21"}
framework: {name: spring-boot, version: "3.3.0"}
```

HUSL Specification:
[Paste domain-registry.husl.md]

BEGIN ANALYSIS:
```
