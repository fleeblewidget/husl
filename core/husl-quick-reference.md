# HUSL Quick Reference Guide

A one-page reference for the Business Specification Language.

---

## Document Structure

```markdown
# [System Name] Specification

## Overview                          [Required]
  - Metadata (service, version, API style)
  - Free-form description
  - Service Context (for distributed systems)

## Schema                            [Required]
  - Entities (fields, constraints)
  - Enums (values with descriptions)
  - Custom Types (validation rules)
  - Computed Fields

## State Machines                    [Optional - use when 3+ states]
  - States and transitions
  - Allowed operations per state

## Rules                             [Optional]
  - Business rules
  - Authorization policies
  - Validation logic

## Operations                        [Required]
  - API endpoints
  - Input/output
  - Preconditions/effects
  - Error cases
  - Tests

## Events                            [Optional - for event-driven]
  - Event definitions
  - Payloads and consumers

## Background Jobs                   [Optional - for scheduled tasks]
  - Cron jobs
  - Event-driven jobs

## Cross-Cutting Concerns            [Optional]
  - Audit logging
  - Notifications, etc.

## Edge Cases and TODOs              [Optional]
## Future Considerations             [Optional]
## Version History                   [Optional]
```

---

## Quick Templates

### Entity
```markdown
Entity: [Name]
  field: Type (required/optional, constraints, description)
  
Constraints:
  - Human-readable constraint

[Optional: State Machine: See [Name] Lifecycle]
```

### Enum
```markdown
[Name] Enum:
  value1  - Description
  value2  - Description
```

### Operation (Simple)
``````markdown
### Operation: [Name]

**Description:** Brief description

**Endpoint:** `METHOD /path`

**Input:**
```
field: Type (constraints)
```

**Output:**
```
{
  success: boolean
  data: Type
  error: string
}
```

**Preconditions:**
- Condition 1
- Condition 2

**Effects:**
- Effect 1
- Effect 2

**Error Cases:**
- Condition → error: "Message", errorCode: CODE, httpStatus: 400

**SLA:**
- Latency: target
- Criticality: high/medium/low

**Tests:**

Test: [Name]
```
given:
  entity: {field: value}

when: Operation

then:
  assertion = expected
```
``````

### Rule
``````markdown
**Rule: [Name]**

```
description: "What this rule enforces"

When: [Condition]
  Then: [Outcome]

Reasoning: Why this rule exists
```
``````

### State Machine
``````markdown
### [Entity] Lifecycle

**States:**
```
State1: description
State2: description
```

**Transitions:**
```
State1 → State2
  via: Operation
  conditions: [list]
  effects: [list]
```

**Allowed Operations by State:**
```
State: State1
  allowed: Op1, Op2
  prohibited: Op3 (reason)
```
``````

### Background Job
``````markdown
### Job: [Name]

**Description:** What it does

**Trigger:**
  - Schedule: Every X minutes (cron: expression)

**Scope:**
  - Processes: Entity where condition

**Logic:**
```
For each Entity where condition:
  If: condition
    Then: action
```

**Side Effects:**
- Effect 1
- Effect 2
``````

### Test
``````markdown
Test: [Name]
```
given:
  entity: {field: value}
  user: {role: Role}
  input: {field: value}

when: Operation(params)

then:
  success = true
  entity.field = value
  collection contains item
```
``````

---

## Common Patterns

### Authorization Check
```markdown
**Preconditions:**
- If user.role = Registrar: user.registrarId must equal entity.registrar
- If user.role = RegistryOperator: no restriction
```

### State-Based Operation Restriction
```markdown
**Preconditions:**
- Entity status must be Active
- Cannot perform operation when status is Deleted/Expired
```

### Audit Logging (Cross-Cutting)
```markdown
**Effects:**
- Audit log entry created:
  - operation: OPERATION_NAME
  - userId: user.id
  - details: "Description"
  - If user.role = RegistryOperator: append ", operator={user.id}"
```

### Error with Code
```markdown
**Error Cases:**
- Entity not found →
  - error: "Entity not found"
  - errorCode: ENTITY_NOT_FOUND
  - httpStatus: 404
```

---

## Field Constraints Quick Reference

```markdown
required                    - Must have value
optional                    - May be null
immutable                   - Cannot change after creation
system-generated            - System provides value
defaults: value             - Default if not provided
minimum: n                  - Min items (collections)
maximum: n                  - Max items (collections)
references Entity.field     - Foreign key
format: description         - Expected format
only if condition           - Conditional requirement
```

---

## Type System

```
Built-in:        Custom:           Collections:
- string         - DomainName      - List<Type>
- boolean        - UserId          - Set<Type>
- number         - Email           - Map<K,V>
- Date           - [Your types]
- Timestamp
- UUID
```

---

## Naming Conventions

```
Entities:        PascalCase      Domain, Lock
Fields:          camelCase       domainName, createdAt
Enums (type):    PascalCase      LockType, UserRole
Enum values:     camelCase       registrarLock, activeStatus
Operations:      PascalCase      CreateDomain, ApplyLock
Rules:           PascalCase      RegistrarOwnership
Error codes:     UPPER_SNAKE     DOMAIN_NOT_FOUND
```

---

## When to Use Optional Sections

**Service Context** → Distributed systems with multiple services

**State Machines** → Entity has 3+ states with different operation permissions

**Rules** → Complex business logic used across operations

**Events** → Event-driven architecture or async workflows

**Background Jobs** → Scheduled tasks or cron jobs

**Cross-Cutting Concerns** → Behavior that applies to many operations (audit, notifications)

---

## Separate Configuration Files

### stack.yml
```yaml
language: {name: java, version: "17"}
framework: {name: spring-boot, version: "3.2.0"}
database: {type: postgresql, orm: jpa}
api: {style: rest, format: json}
```

### standards.yml
```yaml
project: {groupId: com.example, basePackage: com.example.service}
packages: {models: models, services: services, controllers: controllers}
naming: {services: PascalCase + Service}
architecture: {pattern: layered}
```

---

## Version History Entry

```markdown
### Version X.Y.Z - YYYY-MM-DD

**Type:** Major/Minor/Patch

**Changes:**
- Change 1
- Change 2

**Migration Notes:**
- What needs to change

**Rationale:**
- Why this change
```

**Versioning:**
- MAJOR = Breaking changes
- MINOR = New features (backwards compatible)
- PATCH = Bug fixes, clarifications

---

## Comments & TODOs

```markdown
# TODO: Question or decision needed
# NOTE: Important clarification
# CRITICAL: Must not be missed
# Additional tests: [list]
# Similar operations: [pattern description]
```

---

## LLM Extraction Notes

When extracting from code, mark:
- `# TODO: INFERRED - needs review` - Uncertain extraction
- `# TODO: AMBIGUOUS in code` - Unclear logic
- `# ARCHITECTURAL OBSERVATIONS:` - Potential improvements

---

## Development Workflow

**Spec-First:**
1. Write HUSL spec
2. Generate code
3. Deploy

**Code-First:**
1. Extract spec from code
2. Review and fix
3. Regenerate going forward

**Changes:**
Update spec → Regenerate code → Review → Deploy

---

## Key Principles

✓ Natural language over code syntax  
✓ Progressive detail (simple operations brief, complex detailed)  
✓ Explicit over implicit  
✓ Capture uncertainty with TODOs  
✓ Spec is source of truth  
✓ Stack-agnostic (portable)

---

**For full details, see:** `bsl-language-definition.md`
