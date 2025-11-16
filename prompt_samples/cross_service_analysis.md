## Cross-Service Analysis

### Purpose
Analyze multiple service specifications to identify architectural issues and improvement opportunities.

### Prompt Template

```
You are a senior software architect analyzing a distributed system to identify architectural improvements.

CONTEXT:
You have been provided with HUSL specifications for multiple microservices in a distributed system. Your goal is to analyze the architecture holistically and identify issues, coupling problems, and opportunities for improvement.

SERVICES PROVIDED:
[For each service, provide:]

Service: [service-name]
[Paste complete .husl.md file]

---

ANALYSIS REQUIREMENTS:

Analyze the system for:

1. **Service Coupling:**
   - Tight coupling (excessive synchronous calls between services)
   - Circular dependencies
   - Chatty communication patterns
   - Services that should be merged
   - Services that should be split

2. **Bounded Context Violations:**
   - Services modifying each other's data directly
   - Shared database tables across services
   - Entities that belong in wrong service
   - Missing aggregate boundaries

3. **Duplicated Logic:**
   - Business rules duplicated across services
   - Validation logic repeated
   - Authorization patterns duplicated
   - Opportunities for shared libraries

4. **Missing Event-Driven Patterns:**
   - Synchronous calls that should be async
   - Operations that should publish events
   - Missing event-driven workflows
   - Polling that should be event-based

5. **Consistency Boundaries:**
   - Cross-service invariants
   - Missing compensating transactions
   - Potential distributed transaction issues
   - Eventual consistency concerns

6. **Performance Issues:**
   - N+1 query problems across services
   - Missing caching opportunities
   - Inefficient data fetching patterns
   - Unnecessary service hops

7. **Data Flow:**
   - Map complete data flows across services
   - Identify bottlenecks
   - Find redundant data transfers

OUTPUT FORMAT:

# Cross-Service Architecture Analysis

## Executive Summary

**Services Analyzed:** [count]
**Critical Issues:** [count]
**Warnings:** [count]
**Suggestions:** [count]

**Top 3 Recommendations:**
1. [Most impactful improvement]
2. [Second most impactful]
3. [Third most impactful]

---

## Service Dependency Graph

```
[Visual representation of service dependencies]

[ServiceA] --sync--> [ServiceB]
           --async-> [ServiceC]
[ServiceB] --sync--> [ServiceD]
```

**Analysis:**
- [Observations about dependencies]
- [Coupling concerns]

---

## Issues Found

### Critical Issues (requires immediate attention)

#### Issue 1: [Title]

**Severity:** Critical  
**Impact:** [Business/technical impact]

**Description:**
[Detailed description of the issue]

**Evidence:**
- [Service A]: [Specific observation from spec]
- [Service B]: [Specific observation from spec]

**Recommendation:**
[Specific, actionable recommendation]

**Effort:** [Low | Medium | High]  
**Risk:** [Low | Medium | High]

**Implementation Approach:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

---

### Warnings (should be addressed)

[Same format as Critical Issues]

---

### Suggestions (nice to have)

[Same format as Critical Issues]

---

## Architectural Patterns Analysis

### Current Patterns Detected:
- [Pattern 1]: Used in [services]
- [Pattern 2]: Used in [services]

### Recommended Patterns:
- [Pattern]: Would benefit [services] because [reason]

---

## Bounded Context Assessment

### Well-Defined Contexts:
- [Service]: Owns [entities], clear boundaries

### Context Violations:
- [Service A] modifies [Service B]'s [entity]
  - Recommendation: [How to fix]

---

## Event-Driven Opportunities

### Current Event Usage:
- [Count] events published
- [Count] event subscribers

### Recommended Event Additions:

**Event:** [EventName]  
**Publisher:** [ServiceA]  
**Subscribers:** [ServiceB, ServiceC]  
**Benefits:**
- Decouples [A] from [B]
- Reduces latency by [amount]
- Enables [new capability]

---

## Performance Hotspots

### Identified Bottlenecks:

**Operation:** [ServiceA.Operation] → [ServiceB.Operation]  
**Current Flow:**
```
User → ServiceA → ServiceB (3 calls) → ServiceC → ServiceA
Total latency: ~500ms
```

**Recommendation:**
```
User → ServiceA → (async event) → ServiceB
Total latency: ~100ms
ServiceB processes asynchronously
```

**Impact:** 80% latency reduction

---

## Data Consistency Analysis

### Invariants Across Services:

**Invariant:** [Description]  
**Scope:** [ServiceA, ServiceB]  
**Current Implementation:** [How it's maintained]  
**Risk:** [Consistency concerns]  
**Recommendation:** [How to improve]

---

## Migration Roadmap

### Phase 1: Critical Fixes (Week 1-2)
- [Fix 1]
- [Fix 2]

### Phase 2: Architectural Improvements (Week 3-6)
- [Improvement 1]
- [Improvement 2]

### Phase 3: Optimizations (Week 7-10)
- [Optimization 1]
- [Optimization 2]

---

## Metrics to Track

Before and after improvements, track:
- Service-to-service call volume
- P95 latency per endpoint
- Error rates
- Event processing lag
- Database query counts

---

ANALYSIS GUIDELINES:

- Be specific: Reference exact operations and entities from specs
- Prioritize: Focus on highest-impact issues first
- Be practical: Consider effort vs. benefit
- Provide examples: Show before/after patterns
- Consider trade-offs: Note any downsides to recommendations
- Think holistically: How changes affect entire system

BEGIN ANALYSIS:
```

### Usage Example

```
You are a senior software architect analyzing a distributed system...

[... paste full prompt ...]

SERVICES PROVIDED:

Service: domain-registry-service
[Paste domain-registry-service.husl.md]

---

Service: billing-service
[Paste billing-service.husl.md]

---

Service: notification-service
[Paste notification-service.husl.md]

BEGIN ANALYSIS:
```
