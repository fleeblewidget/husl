# Human-Understandable Specification Language (HUSL) - Language Definition

**Version:** 1.0.0  
**Created:** 2025-11-03  
**Purpose:** A structured markdown-based language for defining software system behavior that is both human-readable and machine-parseable for code generation.

---

## Design Principles

1. **Natural language over code syntax** - Readable by non-technical stakeholders
2. **Progressive detail** - Simple operations can be brief, complex ones detailed
3. **Business logic as first-class** - Not buried in implementation
4. **Explicit over implicit** - Clear definitions and constraints
5. **Capture uncertainty** - TODOs and open questions are part of the spec
6. **Portable** - Stack-agnostic, can target multiple languages/frameworks

---

## Document Structure

A HUSL document consists of these top-level sections:

```markdown
# [System Name] Specification

## Overview
## Schema
## State Machines (optional)
## Rules (optional)
## Operations
## Events (optional)
## Background Jobs (optional)
## Cross-Cutting Concerns (optional)
## Edge Cases and TODOs (optional)
## Future Considerations (optional)
## Version History (optional)
```

---

## Section Definitions

### Section: Overview

**Purpose:** High-level description of what the system does, key assumptions, and service context.

**Format:**
```markdown
## Overview

**Metadata:**
- Service: [service-identifier]
- Version: [semver]
- API Style: [rest | grpc | graphql]
- Communication: [sync | async | hybrid]

[Free-form text describing the system, its purpose, and any important context]

Key assumptions:
- [Assumption 1]
- [Assumption 2]

### Service Context (optional - for distributed systems)

**Service Name:** [ServiceName]
**Domain:** [Business domain this service owns]

**Responsibilities:**
- [Core responsibility 1]
- [Core responsibility 2]

**Dependencies:**
```yaml
[ServiceName]:
  purpose: [Why we depend on this service]
  operations: [Operation1, Operation2]
  data: [Entity1, Entity2]
  coupling: [sync | async | event-driven]
```

**Provides:**
```yaml
[ConsumerService]:
  operations: [Operation1, Operation2]
  data: [Entity1, Entity2]
  interface: [rest | grpc | events]
```

**Owned Entities:** [Entity1, Entity2]  
**Referenced Entities:** [Entity1@ServiceName, Entity2@ServiceName]

**Published Events:** [Event1, Event2] (if event-driven)  
**Subscribed Events:** [Event1, Event2] (if event-driven)
```

**When to use Service Context:**
- Building distributed systems with multiple services
- Need to document service boundaries and dependencies
- Helpful for both humans and LLMs to understand architecture

---

### Section: Schema

**Purpose:** Define all entities, enums, and types used throughout the specification.

#### Entity Definitions

**Format:**
```markdown
Entity: [EntityName]
  [fieldName]: [Type] ([constraints], [description])
  [fieldName]: [Type] ([constraints], [description])
  ...

Constraints:
  - [Human-readable constraint description]
  - [Another constraint]

[Optional: State Machine: See [EntityName] Lifecycle]

[Optional additional notes or context]
```

**Field Constraint Keywords:**
- `required` - Field must have a value
- `optional` - Field may be null/empty
- `immutable` - Field cannot be changed after creation
- `system-generated` - System provides the value automatically
- `defaults: [value]` - Default value if not provided
- `minimum: [n]` - For lists/collections, minimum number of items
- `maximum: [n]` - For lists/collections, maximum number of items
- `references [Entity].[field]` - Foreign key relationship
- `format: [format description]` - Expected format (UUID, ISO 8601, etc.)
- `only if [condition]` - Conditional requirement
- Free-form description in parentheses

**Type System:**

Built-in types:
- `string` - Text data
- `boolean` - true/false
- `number` - Numeric data (optionally: integer, decimal)
- `Date` - Calendar date (format: YYYY-MM-DD)
- `Timestamp` - Date and time (format: ISO 8601)
- `UUID` - Universal unique identifier (format: UUID v4)
- `List<Type>` - Array/collection of Type
- Custom types defined as Entities or Enums

**Example:**
```markdown
Entity: Lock
  id: UUID (required, immutable, system-generated)
  domainName: DomainName (required, immutable, references Domain.name)
  type: LockType (required, immutable, determines level and who can apply)
  statuses: List<StatusName> (required, minimum: 1, the prohibited statuses this lock applies)
  appliedBy: User.id (required, immutable, who created the lock)
  appliedAt: Timestamp (required, immutable, system-generated, when the lock was created)
  removedAt: Timestamp (optional, when the lock was removed, must be after appliedAt)
  temporaryUnlockUntil: Timestamp (optional, only if type = registryLock, when temporary unlock expires)

Constraints:
  - Only one lock of each type can exist per domain at a time (unique on domainName + type where removedAt is null)
  - temporaryUnlockUntil only valid when type = registryLock
  - If removedAt is set, removedBy must also be set

State Machine: See Lock Lifecycle
```

#### Enum Definitions

**Format:**
```markdown
[EnumName] Enum:
  [VALUE1]           - [Description]
  [VALUE2]           - [Description]
  ...

[Optional additional context]
```

**Example:**
```markdown
LockType Enum:
  registrarLock           - Standard client lock (level: registrar)
  registryLock            - Premium client lock with server-level protections (level: registrar)
  secureLock              - Standard server lock (level: registryOperator)
  legalLock               - Legal hold lock (level: registryOperator)

Notes:
  - registrarLock and registryLock can be applied by registrars
  - All other types require RegistryOperator role
```

#### Computed/Derived Fields

**Format:**
```markdown
[FieldName] (computed from [SourceField]):
  formula: [Description of how it's computed]
  
OR

[FieldName] (derived from [SourceField]):
  [value1] → [condition1]
  [value2] → [condition2]
  otherwise → [default value]
```

**Example:**
```markdown
LockLevel (derived from LockType):
  registrar → if type IN [registrarLock, registryLock]
  registryOperator → otherwise
```

#### Custom Type Definitions

For types that need validation rules beyond basic types:

**Format:**
```markdown
Type: [TypeName]
  base_type: [string | number | etc]
  description: [What this type represents]
  
  constraints:
    length:
      minimum: [n]
      maximum: [n]
    character_sets:
      allowed: [description]
      not_allowed: [description]
    structure:
      - [Rule 1]
      - [Rule 2]
    [other constraint categories as needed]
  
  examples:
    valid: ["example1", "example2"]
    invalid:
      - value: "bad-example"
        reason: "why it's invalid"
```

**Example:**
```markdown
Type: DomainName
  base_type: string
  description: A valid fully-qualified domain name
  
  constraints:
    length:
      minimum: 3
      maximum: 255
    character_sets:
      allowed: lowercase letters (a-z), digits (0-9), hyphens (-), dots (.)
      not_allowed: spaces, uppercase, special characters
    structure:
      - cannot start with a hyphen or dot
      - cannot end with a hyphen or dot
      - cannot contain consecutive dots (..)
      - must contain at least one dot (for subdomains)
  
  examples:
    valid: ["example.uk", "my-domain.com", "test123.co.uk"]
    invalid:
      - value: "-example.uk"
        reason: "starts with hyphen"
      - value: "example..uk"
        reason: "consecutive dots"
      - value: "EXAMPLE.UK"
        reason: "contains uppercase"
```

---

### Section: State Machines (Optional)

**Purpose:** Define state transitions and valid operations per state for entities with complex lifecycle rules.

**When to use:**
- Entity has 3+ distinct states with different allowed operations
- Complex lifecycle with automatic transitions
- State-dependent business rules that are easier to understand as a state machine

**Format:**

``````markdown
## State Machines

### [EntityName] Lifecycle

**Description:** [Overview of the state machine and what it governs]

**States:**
```
[StateName]:
  description: [What this state means]
  entry conditions: [How entity enters this state]
  
[AnotherStateName]:
  description: [What this state means]
  entry conditions: [How entity enters this state]
```

**Initial State:** [Which state new entities start in]

**State Transitions:**

```
[SourceState] → [TargetState]
  via: [OperationName] OR [Background job] OR [Event]
  conditions:
    - [Condition 1]
    - [Condition 2]
  effects:
    - [Side effect 1]
    - [Side effect 2]
```

**Allowed Operations by State:**

```
State: [StateName]
  allowed operations:
    - [Operation1]: [any special conditions or notes]
    - [Operation2]: [any special conditions or notes]
  
  prohibited operations:
    - [Operation3]: [why it's prohibited]
    - [Operation4]: [why it's prohibited]
```

**State-Specific Business Rules:**

```
When: [entity] is in [StateName]
  Then: [Rule or constraint that applies]
```

**Diagram (Optional):**
```
[ASCII art or mermaid diagram reference]
```

**Tests:**

Test: [TestName]
```
given:
  [entity] in state [StateName]
  [other preconditions]

when: [Operation or event]

then:
  [entity] transitions to [TargetState]
  [other assertions]
```
``````

---

### Section: Rules (Optional)

**Purpose:** Define business rules, authorization policies, and validation logic that apply across operations.

**Format:**
``````markdown
### [Category Name] (optional grouping)

**Rule: [RuleName]**

```
description: [Human-readable description]

[Optional context: Critical:, Exception to:, Reasoning:, Status:, TODO:]

When: [Condition or trigger]
  Then: [Outcome or consequence]
  [Optional: additional When/Then clauses]

[Optional sections:]
Validation:
  - [What to check and what error to return]

Implementation:
  [Optional pseudocode or detailed logic]

Examples:
  [Optional concrete scenarios]
```
``````

**Rule Context Keywords:**
- `Critical:` - Emphasizes importance or override behavior
- `Exception to: [RuleName]` - Documents exceptions to other rules
- `Reasoning:` - Explains why the rule exists
- `Status:` - Rule status (approved, pending, experimental)
- `TODO:` - Open questions or decisions needed

---

### Section: Operations

**Purpose:** Define the API operations/endpoints and their behavior.

**Level of Detail Guidelines:**

**Simple operations** (basic CRUD, straightforward logic):
- Brief description
- Input/output can be minimal
- Preconditions as simple list
- Effects as simple list
- 1-3 key test cases

**Complex operations** (business logic, multiple paths, authorization):
- Detailed description
- Full input/output schemas with constraints
- Structured preconditions with sub-conditions
- Detailed effects with field-level breakdown
- Comprehensive error cases with messages and codes
- 5-10+ test cases covering all paths

**Format:**

``````markdown
### Operation: [OperationName]

**Description:** [Brief human-readable description of what this operation does]

**Endpoint:** [HTTP_METHOD] [/path/with/{parameters}]

**Input:**
```
[Optional: Path parameters:]
  [paramName]: [Type] ([constraints], [description])

[Optional: Body:]
  [fieldName]: [Type] ([constraints], [description])
  
[Optional: Query parameters:]
  [paramName]: [Type] ([constraints], [description])

[Optional: Headers:]
  [headerName]: [Type] ([constraints], [description])
```

**Output:**
```
Success response:
{
  success: true
  [fieldName]: [Type] ([description])
  ...
}

Error response:
{
  success: false
  error: string (human-readable error message)
  errorCode: string (machine-readable error code)
}

[Optional: Detailed object schemas]
[ObjectName] object (when [condition]):
{
  [fieldName]: [Type] ([description])
  ...
}
```

**Preconditions:**
- [Condition 1]
- [Condition 2 with details]
  - [Sub-condition if needed]
- ...

**Effects:**
- [Effect 1]
- [Effect 2 with structure]:
  - [field]: [how it's set]
  - [field]: [how it's set]
- ...

**Error Cases:**
- [Error condition] →
  - error: "[Error message with {placeholders}]"
  - errorCode: [ERROR_CODE]
  - httpStatus: [HTTP status code]
  - [Optional: When: additional context]
  - [Optional: Note: clarification]

**SLA:**
- Latency: [target latency description]
- Availability: [target percentage]
- Criticality: [low | medium | high]

**Tests:**

Test: [TestName]
```
given:
  [precondition]: [value or object literal]
  [another precondition]: [value]
  ...

when: [OperationName] OR [OperationName]([parameters])

then:
  [assertion] = [expected value]
  [object].[field] = [expected value]
  [assertion about system state]
  ...
```
``````

---

### Section: Events (Optional)

**Purpose:** Define events for event-driven architectures and cross-service coordination.

**When to use:**
- Event-driven architectures
- Async workflows or cross-service coordination
- Publishing domain events
- LLMs may suggest adding this during architectural review

**Format:**

``````markdown
## Events

### Event: [EventName]

**Description:** [What this event represents]

**Trigger:** [What causes this event to be published]
  - Operation: [OperationName]
  - Condition: [When the event fires]

**Payload:**
```
{
  eventId: UUID (unique event identifier)
  eventType: string (event type name)
  timestamp: Timestamp (when event occurred)
  aggregateId: string (identifier of the entity)
  aggregateType: string (type of entity)
  payload: {
    [field]: [Type] ([description])
    ...
  }
  metadata: {
    userId: string (who triggered the event)
    correlationId: UUID (for tracing)
  }
}
```

**Consumers:**
- [ServiceName]: [How they use this event]

**Guarantees:**
- Delivery: [at-least-once | at-most-once | exactly-once]
- Ordering: [ordered | unordered]
```

---

### Section: Background Jobs (Optional)

**Purpose:** Define scheduled, periodic, or event-driven background processes.

**When to use:**
- Scheduled/periodic tasks independent of user actions
- Event-driven processes triggered by system events
- Cleanup, expiry, or maintenance tasks

**Format:**

```markdown
## Background Jobs

### Job: [JobName]

**Description:** [What this job does and why]

**Trigger:** [When/how this job runs]
  - Schedule: [cron expression or natural language]
  OR
  - Event: [event that triggers this job]
  OR
  - Condition: [condition that triggers execution]

**Scope:** [What this job operates on]
  - Processes: [description of what entities/records are affected]
  - Selection criteria: [how to find entities to process]

**Logic:**
```
For each [entity] where [condition]:
  If: [condition]
    Then: [action]
  Else if: [condition]
    Then: [action]
  Otherwise:
    [default action]
```

**Side Effects:**
- [Effect 1]
- [Effect 2]
- Audit log: [what gets logged]

**Error Handling:**
- [How errors are handled]
- [Retry policy if applicable]

**SLA:**
- Frequency: [how often it runs]
- Max duration: [how long it should take]
- Criticality: [low | medium | high]

**Tests:**

Test: [TestName]
```
given:
  [initial state]
  current time: [timestamp]
  job last ran at: [timestamp]

when: [JobName] runs

then:
  [expected outcomes]
```
``````

---

### Section: Cross-Cutting Concerns (Optional)

**Purpose:** Define system-wide behaviors that apply to multiple operations.

**Format:**
```markdown
## Cross-Cutting Concerns

### [Concern Name]

[Description of the concern]

**Applies to:** [Which operations or conditions]

**Behavior:**
[Structured description of what happens]

**Example:**
[Optional example showing how it works]
```

**Example:**
```markdown
## Cross-Cutting Concerns

### Audit Logging

All write operations (Create, Update, Delete, Apply, Remove) generate audit log entries.

**Applies to:** Any operation that modifies system state

**Behavior:**
- Audit log entry created with:
  - timestamp: current time (ISO 8601)
  - userId: user.id from authentication context
  - operation: operation type (CREATE, UPDATE, DELETE, etc.)
  - resourceType: type of resource modified
  - resourceId: identifier of the resource
  - details: operation-specific details (free-form string)
- If user.role = RegistryOperator: append ", operator={user.id}" to details field
- Audit logs are immutable and retained for compliance purposes
```

---

### Section: Edge Cases and TODOs (Optional)

**Purpose:** Document known issues, open questions, and decisions that need to be made.

**Format:**
```markdown
## Edge Cases and TODOs

1. **[Topic Name]:**
   - [Question or uncertainty]
   - [Possible approaches or implications]
   - TODO: [What needs to be decided]
```

---

### Section: Future Considerations (Optional)

**Purpose:** Document potential future enhancements or expansions.

**Format:**
```markdown
## Future Considerations

1. **[Feature Name]:** [Brief description]
2. **[Another Feature]:** [Brief description]
```

---

### Section: Version History (Optional)

**Purpose:** Track changes to the specification over time using semantic versioning.

**Format:**

```markdown
## Version History

**Current Version:** [MAJOR.MINOR.PATCH]

**Versioning Rules:**
- MAJOR: Breaking changes to operations (changed/removed endpoints, incompatible input/output)
- MINOR: New operations, new optional fields, backwards-compatible enhancements
- PATCH: Bug fixes, clarifications, documentation improvements, non-functional changes

---

### Version [X.Y.Z] - [YYYY-MM-DD]

**Type:** [Major | Minor | Patch]

**Changes:**
- [Change 1]
- [Change 2]

**Migration Notes:** (for Major/Minor versions)
- [What clients need to change]
- [Deprecation timeline]

**Rationale:** (optional)
[Why this change was made]
```

---

## Test Format Specification

Tests use a Given/When/Then structure:

``````
Test: [TestName]
```
given:
  [entity type]: [object literal or value]
  [another entity]: [object literal or value]
  input: [input object] (optional, for operations with explicit input)
  header: [HeaderName]: [value] (optional, for header-based inputs)

when: [OperationName] OR [OperationName]([parameters])

then:
  [assertion] = [expected value]
  [entity].[field] = [expected value]
  [entity].[field] contains [value]
  [collection] contains [item]
  [collection] contains entry [with properties]
  no [entity] created
  [entity] unchanged
```
``````

**Object Literal Format:**
```
{field1: value1, field2: value2, nested: {subfield: value}}
```

**Assertions:**
- `=` - Equality
- `contains` - Collection membership or substring
- `unchanged` - State did not change
- `no X created` - Entity was not created
- Any natural language assertion that clearly expresses expected outcome

---

## Language Conventions

### Naming
- **Entities:** PascalCase (e.g., `Domain`, `Lock`, `User`)
- **Fields:** camelCase (e.g., `domainName`, `appliedAt`, `registrarId`)
- **Enums:** PascalCase for type, camelCase for values (e.g., `LockType`, `registrarLock`)
- **Operations:** PascalCase (e.g., `ApplyLock`, `CreateDomain`)
- **Rules:** PascalCase (e.g., `RegistryLockAlwaysRemovable`)
- **Error Codes:** UPPER_SNAKE_CASE (e.g., `DOMAIN_NOT_FOUND`)

### Comments and TODOs
- Use `# TODO:` for inline questions in any section
- Use `# NOTE:` for important clarifications
- Use `# CRITICAL:` for critical behavior that must not be missed

### References
- Reference other rules: `(see [RuleName] rule)` or `(per [RuleName] rule)`
- Reference entities: Use entity name with optional field: `Domain.status`, `User.role`
- Reference operations: Use operation name: `(see CreateDomain operation)`

---

## Stack Configuration (Separate Files)

HUSL specs should be stack-agnostic. Technology choices are defined in separate configuration files:

### `stack.yml` - Technology Stack

Defines implementation technologies for code generation:

```yaml
version: "1.0"

language:
  name: java
  version: "17"

framework:
  name: spring-boot
  version: "3.2.0"

build:
  tool: maven

database:
  type: postgresql
  version: "15"
  orm: jpa

api:
  style: rest
  format: json
  
testing:
  unit: junit5
  integration: spring-boot-test
  mocking: mockito
```

### `standards.yml` - Code Conventions

Defines how generated code should be structured:

```yaml
version: "1.0"

project:
  groupId: com.example
  artifactId: service-name
  basePackage: com.example.service

packages:
  models: ${basePackage}.models
  services: ${basePackage}.services
  controllers: ${basePackage}.controllers
  repositories: ${basePackage}.repositories

naming:
  services: PascalCase + "Service" suffix
  controllers: PascalCase + "Controller" suffix
  tests: PascalCase + "Test" suffix

code_style:
  line_length: 100
  indent: 2

architecture:
  pattern: layered
  service_layer: required
  repository_layer: required
```

**Benefits of separation:**
- Same spec can target Java/Spring, Python/FastAPI, TypeScript/NestJS, etc.
- Stack upgrades are just config changes
- Clear separation of "what" (spec) vs "how" (stack/standards)

---

## HUSL Extraction Guidelines (for LLMs)

When extracting HUSL specs from existing code:

### Target Fidelity: 100% accuracy for business logic

**Always extract completely:**
- Entity schemas with all fields and constraints
- Enums with all values
- Business rules and authorization logic
- State machines and transitions
- Error cases with messages
- Core operation behavior (preconditions, effects)

**Summarize when verbose:**
- Tests: Include 2-3 representative tests per category
  - Add note: `# Additional tests in codebase: [list test names]`
- Similar operations: Fully document 1-2 complex, summarize simple CRUD
  - Add note: `# Similar operations: GetX, ListX follow standard CRUD pattern`
- Repeated patterns: Document once, reference elsewhere

**Mark uncertainty:**
- `# TODO: INFERRED - needs review` for uncertain extractions
- `# TODO: AMBIGUOUS in code` for unclear logic
- `# NOTE: Multiple implementations found` when code has inconsistencies

**Flag architectural issues:**
```yaml
# ARCHITECTURAL OBSERVATIONS:
# - Missing transaction boundaries in CreateDomain + ChargeFee
# - Tight coupling: 5 synchronous calls to billing-service
# - Consider: Event-driven pattern for non-critical notifications
```

---

## Development Workflow

### Greenfield (Spec-First):

1. Write HUSL spec
2. Review with stakeholders
3. Generate code from spec + stack.yml
4. Implement infrastructure
5. Deploy

### Brownfield (Code-First):

1. LLM extracts HUSL spec from code
2. Human reviews and fixes TODOs
3. Spec becomes source of truth
4. Regenerate code from spec
5. Future changes: Update spec → Regenerate

### Change Management:

**Rule:** Spec is always the source of truth

- Business logic changes: Update spec → Regenerate code
- Infrastructure changes: Update stack.yml → Regenerate
- Hotfixes: Fix code → Update spec ASAP → Validate with regeneration

---

## Stack Upgrade Analysis

When upgrading stack.yml versions, LLMs should generate a Stack Upgrade Report analyzing:

1. **New Features Available** - Language/framework features that could improve current code
2. **Impact Analysis** - Specific examples from the spec showing before/after
3. **Effort Estimation** - Low/Medium/High for each opportunity
4. **Risk Assessment** - Likelihood and impact of potential issues
5. **Recommendations** - Prioritized (High/Medium/Low) with rationale
6. **Migration Plan** - Concrete timeline and steps
7. **Testing Strategy** - How to validate the upgrade

This turns stack upgrades from maintenance chores into improvement opportunities.

---

## Validation and Tooling

Tools should validate:
1. **Schema consistency** - All referenced entities/fields are defined
2. **Type consistency** - Field types match their definitions
3. **Enum usage** - Only defined enum values are used
4. **Completeness** - Operations reference rules correctly
5. **Naming conventions** - Consistent use of case styles

Tools should infer:
1. **Missing entity fields** - From operation usage and tests
2. **Implicit enums** - From repeated string values
3. **Authorization patterns** - From preconditions across operations

Tools should generate:
1. **Code** - Models, services, controllers, tests
2. **Documentation** - API docs, diagrams
3. **Validation reports** - Inconsistencies and gaps
4. **Stack upgrade reports** - Analysis of upgrade opportunities

---

## File Organization

Recommended project structure:

```
project/
├── spec/
│   ├── domain-crud.husl.md         # Feature specs
│   ├── locks.husl.md
│   └── transfers.husl.md
├── stack.yml                       # Technology stack
├── standards.yml                   # Code conventions
└── generated/
    └── [code generated from specs]
```

For large systems, consider:
- Breaking specs into multiple files by domain/feature
- Master spec that references sub-specs
- Shared type definitions in common spec

---

## Appendix: Complete Example

See the included example files:
- `rpp_domain_crud_spec.md` - Full operation specification
- `locks_specification.md` - Complex business rules and state machines
- `stack.yml` - Technology stack configuration
- `standards.yml` - Code conventions

---

**End of HUSL Language Definition v1.0.0**
