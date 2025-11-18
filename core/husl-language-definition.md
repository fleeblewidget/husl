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
## Custom Implementation (optional)
## Edge Cases and TODOs (optional)
## Future Considerations (optional)
## Version History (optional)
```

---

## Code Generation Philosophy

HUSL follows a **hybrid generation model** where:
- The specification is the **single source of truth** for business logic
- Generated code is the **default implementation**
- Custom code is **preserved** in protected regions during regeneration

This balances the purity of spec-driven development with the pragmatic reality that some code needs manual customization (performance optimizations, integration with legacy systems, edge cases not worth specifying).

**Key Principle:** Update spec → Regenerate → Custom code preserved

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
Entity: Order
  id: UUID (required, immutable, system-generated)
  customerId: UUID (required, immutable, references Customer.id)
  status: OrderStatus (required, current state of the order)
  items: List<OrderItem> (required, minimum: 1, products in this order)
  totalAmount: Money (required, total price including tax and shipping)
  shippingAddress: Address (required, where to deliver the order)
  createdAt: Timestamp (required, immutable, system-generated, when order was placed)
  updatedAt: Timestamp (required, system-generated, when order was last modified)
  shippedAt: Timestamp (optional, when order was shipped)
  deliveredAt: Timestamp (optional, when order was delivered)

Constraints:
  - totalAmount must be positive and match sum of items
  - shippedAt must be after createdAt
  - deliveredAt must be after shippedAt
  - Cannot modify items after status changes to Confirmed

State Machine: See Order Lifecycle
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
OrderStatus Enum:
  pending              - Order placed, awaiting payment confirmation
  confirmed            - Payment received, order confirmed
  processing           - Order being prepared for shipment
  shipped              - Order dispatched to customer
  delivered            - Order successfully delivered
  cancelled            - Order cancelled by customer or system
  refunded             - Payment returned to customer

Notes:
  - Orders can only be cancelled before shipping
  - Refunds can be partial or full
  - Delivered orders can transition to refunded if returned
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
ShippingMethod (derived from totalAmount and shippingAddress):
  standard → if totalAmount < 50 AND shippingAddress.country = "domestic"
  express → if totalAmount >= 50 OR shippingAddress.country != "domestic"
  free → if totalAmount >= 100
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
Type: Email
  base_type: string
  description: A valid email address for customer communications
  
  constraints:
    length:
      minimum: 5
      maximum: 320
    character_sets:
      allowed: letters (a-z, A-Z), digits (0-9), dots (.), hyphens (-), underscores (_), @ symbol
      not_allowed: spaces, multiple consecutive dots
    structure:
      - must contain exactly one @ symbol
      - local part (before @) cannot start or end with a dot
      - domain part (after @) must have at least one dot
      - domain part must be a valid hostname
  
  examples:
    valid: ["customer@example.com", "first.last@shop.co.uk", "user+tag@store.com"]
    invalid:
      - value: "user@example"
        reason: "domain missing TLD"
      - value: "user name@example.com"
        reason: "contains space"
      - value: "@example.com"
        reason: "missing local part"
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

### Section: Custom Implementation (Optional)

**Purpose:** Document where custom code can be added that will be preserved during code regeneration.

**When to use:**
- Performance-critical code that needs hand-optimization
- Integration with legacy systems not worth modeling in spec
- Complex algorithms better written by hand
- Edge cases too specific to include in spec
- Third-party library integrations

**Format:**

``````markdown
## Custom Implementation

### [CustomizationPoint]

**Location:** [Service.method or file path]
**Purpose:** [Why custom implementation is needed]
**Contract:** [What inputs/outputs/behavior must be maintained]

**Hook Type:** [before | after | replace | extend]

**Generated Code Template:**
```[language]
[Show where custom region will be]
```

**Guidelines:**
- [Guideline 1 for implementers]
- [Guideline 2 for implementers]

**Example:**
[Show example custom implementation]
``````

**Hook Types:**

- `before`: Custom code runs before generated logic
- `after`: Custom code runs after generated logic
- `replace`: Custom code completely replaces generated logic (contract must be maintained)
- `extend`: Custom code adds to generated logic (e.g., additional validation)

**Example:**

``````markdown
## Custom Implementation

### FraudDetectionHook

**Location:** OrderService.createOrder (before order confirmation)
**Purpose:** Integration with legacy fraud detection system not modeled in spec
**Contract:** Must return boolean (true = allow order, false = reject order)

**Hook Type:** before

**Generated Code Template:**
```java
public OperationResult<Order> createOrder(CreateOrderRequest request, User user) {
    // Validate input
    validateCreateOrderRequest(request);
    
    // CUSTOM_START: fraud_detection
    // Custom fraud detection logic here
    // Must set: boolean allowOrder
    boolean allowOrder = true; // Default implementation
    // CUSTOM_END: fraud_detection
    
    if (!allowOrder) {
        return OperationResult.failure("Order rejected by fraud detection", "FRAUD_DETECTED");
    }
    
    // Continue with order creation...
}
```

**Guidelines:**
- Fraud check must complete within 500ms (SLA requirement)
- Log all fraud rejections for audit
- Must handle fraud service being unavailable (fail open or closed per config)
- Don't modify request object, only read it

**Example:**
```java
// CUSTOM_START: fraud_detection
FraudCheckResult result = legacyFraudService.checkOrder(
    request.getCustomerId(),
    request.getTotalAmount(),
    request.getShippingAddress()
);
boolean allowOrder = result.isApproved();
if (!allowOrder) {
    auditLog.log("FRAUD_REJECTION", request.getCustomerId(), result.getReason());
}
// CUSTOM_END: fraud_detection
```
``````

---

### Section: Version History (Optional)

**Purpose:** Track changes to the specification over time using semantic versioning.

**Format:**

``````markdown
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

**Refactoring Metadata:** (optional, for tool support)
```yaml
renames:
  entities:
    OldName: NewName
  enums:
    EnumName:
      oldValue: newValue
  fields:
    EntityName:
      oldFieldName: newFieldName
    "*":  # Apply to all entities
      oldFieldName: newFieldName
  operations:
    OldOperationName: NewOperationName

additions:
  EntityName:
    - fieldName: Type (constraints)

removals:
  EntityName:
    - fieldName

modifications:
  EntityName.fieldName:
    old_type: OldType
    new_type: NewType
    reason: [Why this changed]
```
``````

**Example with Refactoring:**

``````markdown
### Version 2.1.0 - 2025-11-20

**Type:** Minor

**Changes:**
- Renamed UserRole.RegistryOperator to UserRole.RegistryAdmin for clarity
- Added Order.trackingNumber field for shipment tracking
- Changed Order.notes from String to List<OrderNote> for better structure

**Migration Notes:**
- Clients checking user.role == "RegistryOperator" must update to "RegistryAdmin"
- Order.notes is now an array; single string values will be migrated automatically

**Rationale:**
"RegistryAdmin" better reflects actual responsibilities. Tracking numbers required for new shipping integration. Structured notes enable better audit trail.

**Refactoring Metadata:**
```yaml
renames:
  enums:
    UserRole:
      registryOperator: registryAdmin

additions:
  Order:
    - trackingNumber: String (optional, format: tracking number from carrier)

modifications:
  Order.notes:
    old_type: String
    new_type: List<OrderNote>
    reason: Enable structured notes with timestamps and authors
```
``````

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
- **Entities:** PascalCase (e.g., `Order`, `Product`, `Customer`)
- **Fields:** camelCase (e.g., `customerId`, `createdAt`, `totalAmount`)
- **Enums:** PascalCase for type, camelCase for values (e.g., `OrderStatus`, `pendingPayment`)
- **Operations:** PascalCase (e.g., `CreateOrder`, `CancelOrder`)
- **Rules:** PascalCase (e.g., `CannotCancelAfterShipping`)
- **Error Codes:** UPPER_SNAKE_CASE (e.g., `ORDER_NOT_FOUND`)

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

code_generation:
  markers:
    generated_file_header: |
      // GENERATED CODE - DO NOT EDIT DIRECTLY
      // Generated from: {spec_file}
      // Generated at: {timestamp}
      // Generator version: {version}
    custom_region_start: "// CUSTOM_START: {region_name}"
    custom_region_end: "// CUSTOM_END: {region_name}"
    custom_region_default: |
      // CUSTOM_START: {region_name}
      // Add custom implementation here
      // CUSTOM_END: {region_name}
  
  regeneration:
    strategy: preserve_custom_regions
    on_conflict:
      - create_backup  # Save existing file as .backup
      - warn_user      # Log warning about conflicts
      - preserve_custom  # Always keep custom regions
    
  protected_patterns:
    - "// CUSTOM_START:*"  # Never overwrite between these markers
    - "# CUSTOM_START:*"   # Language-agnostic patterns
```

**Benefits of separation:**
- Same spec can target Java/Spring, Python/FastAPI, TypeScript/NestJS, etc.
- Stack upgrades are just config changes
- Clear separation of "what" (spec) vs "how" (stack/standards)
- Code generation behavior is explicit and configurable

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

## Code Generation Strategies

### Full Regeneration

```bash
husl generate spec.husl --stack stack.yml --standards standards.yml --output src/
```

**When to use:**
- Initial project setup
- Major spec restructuring
- Complete rebuilds
- Switching technology stacks

**What it does:**
- Generates all code from scratch
- Preserves custom regions marked with CUSTOM_START/END
- Creates backup of existing files
- Reports any conflicts

---

### Selective Regeneration

```bash
husl generate spec.husl --only CreateOrder,CancelOrder --output src/
```

**When to use:**
- Updated specific operations in spec
- Adding new operations
- Modifying subset of entities

**What it does:**
- Regenerates only specified operations and their dependencies
- Faster than full regeneration
- Still preserves custom regions

---

### Incremental Update

```bash
husl update spec.husl
```

**When to use:**
- Small spec changes (field additions, renames)
- Documentation updates
- Non-breaking changes

**What it does:**
- Analyzes diff between last generation and current spec
- Updates only changed parts
- Applies refactoring metadata from version history
- Preserves custom code

---

### Diff Preview

```bash
husl diff spec.husl
# or
husl generate spec.husl --dry-run
```

**When to use:**
- Before committing to regeneration
- Validating spec changes
- Code review preparation

**What it does:**
- Shows what would change without modifying files
- Highlights potential conflicts with custom code
- Estimates impact (files changed, lines added/removed)

---

### Refactoring Mode

```bash
husl refactor spec.husl --apply-metadata
```

**When to use:**
- Large-scale renames (entities, fields, operations)
- Restructuring (moving fields between entities)
- Breaking changes with migration path

**What it does:**
- Reads refactoring metadata from version history
- Applies systematic changes across all code
- Much more efficient than manual find-replace
- Updates imports, references, tests automatically

**Example refactoring metadata usage:**

```yaml
# In spec version history
renames:
  enums:
    UserRole:
      registryOperator: registryAdmin
```

This single entry causes the refactoring tool to:
- Update enum definition
- Update all references in generated code
- Update test files
- Update API documentation
- Generate migration scripts if needed

---

### Custom Region Preservation

When regenerating code, the tool:

1. **Scans existing code** for custom regions:
   ```java
   // CUSTOM_START: region_name
   // ... custom code ...
   // CUSTOM_END: region_name
   ```

2. **Extracts custom code** into memory

3. **Generates new code** from spec

4. **Injects custom code** back into designated regions

5. **Validates** that custom code contract is maintained

6. **Warns** if custom code seems incompatible with new generated code

**Protected region naming convention:**
- Use snake_case for region names
- Be descriptive: `fraud_detection` not `custom1`
- Region names are stable identifiers (don't change them)

---

### Conflict Resolution

If regeneration finds issues:

**Scenario 1: Custom region removed from spec**
```
WARNING: Custom region "legacy_integration" found in code but not in spec
Options:
  1. Keep custom code (add Custom Implementation section to spec)
  2. Remove custom code (confirm deletion)
  3. Move to separate utility file
```

**Scenario 2: Custom code signature mismatch**
```
ERROR: Custom region "validate_payment" has incompatible signature
Expected: boolean validatePayment(Payment payment)
Found: void validatePayment(String paymentId)

Action required: Update custom code to match new contract
```

**Scenario 3: Multiple custom regions with same name**
```
ERROR: Duplicate custom region "logging" found in:
  - OrderService.java:45
  - OrderService.java:123

Action required: Rename one region to be unique
```

---

## Best Practices

### DO:
✅ Always update spec before regenerating  
✅ Use descriptive custom region names  
✅ Document custom regions in spec's Custom Implementation section  
✅ Run `husl diff` before regenerating  
✅ Keep custom regions small and focused  
✅ Use version control (commit before regeneration)  
✅ Write tests for custom code  

### DON'T:
❌ Edit generated code outside custom regions  
❌ Skip spec updates and edit code directly  
❌ Nest custom regions  
❌ Put business logic in custom regions (put in spec instead)  
❌ Use custom regions for "quick fixes" that should be in spec  
❌ Rename custom region markers (breaks regeneration)  

---

## Migration Path: Manual Code → HUSL

If you have existing code and want to adopt HUSL:

**Phase 1: Extract Spec**
```bash
husl extract src/ --output extracted-spec.husl
# LLM analyzes code and generates initial spec
```

**Phase 2: Review & Refine**
- Human reviews extracted spec
- Fixes TODOs and ambiguities
- Adds missing business rules

**Phase 3: Trial Regeneration**
```bash
husl generate extracted-spec.husl --output generated/ --dry-run
diff -r src/ generated/
```

**Phase 4: Identify Custom Code**
- Find hand-written code that can't be generated
- Document in Custom Implementation section
- Mark regions in existing code

**Phase 5: Full Migration**
```bash
# Backup existing code
cp -r src/ src.backup/

# Regenerate from spec
husl generate extracted-spec.husl --output src/

# Restore custom code (marked regions)
husl merge-custom src.backup/ src/
```

**Phase 6: Validation**
- Run all tests
- Compare behavior
- Fix any issues

**Phase 7: Adopt Workflow**
- From now on: spec is source of truth
- All changes go through spec first

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


**End of HUSL Language Definition v1.0.0**
