## Spec Extraction (Code → HUSL)

### Purpose
Extract a HUSL specification from existing codebase to establish spec as source of truth.

### Prompt Template

```
You are a senior software architect extracting a Human-Understandable Specification Language (HUSL) specification from an existing codebase.

CONTEXT:
The Business Specification Language is a structured markdown format for defining software system behavior that is both human-readable and machine-parseable. Your goal is to create a complete, accurate HUSL document that captures all business logic from the code.

INPUTS PROVIDED:
- Programming language: [language]
- Framework: [framework]
- Code files: [list files or paste code]
- Stack configuration (if available): [paste stack.yml]
- Test files (if available): [paste test code]

HUSL LANGUAGE DEFINITION:
[Paste the complete HUSL language definition from bsl-language-definition.md]

TASK:
Extract a complete HUSL specification following the structure defined above.

EXTRACTION REQUIREMENTS:

1. **Target 100% accuracy for business logic:**
   - Extract ALL entity fields with their constraints
   - Extract ALL enum values
   - Extract ALL business rules and authorization logic
   - Extract ALL error cases with their messages
   - Extract ALL operations with complete behavior

2. **Identify structure:**
   - Analyze code to identify entities (models, DTOs)
   - Find enums and constants
   - Detect state machines from status/state fields
   - Identify authorization patterns
   - Map API endpoints to operations
   - Extract validation rules

3. **Generate tests from existing test code:**
   - Convert unit tests to given/when/then format
   - Include representative samples (2-3 per category)
   - Note additional tests at end: "# Additional tests in codebase: [list]"

4. **Summarize when appropriate:**
   - For similar CRUD operations: Document 1-2 fully, summarize rest
   - For boilerplate tests: Include representative samples
   - Add notes: "# Similar operations follow standard CRUD pattern"

5. **Mark uncertainty:**
   - Use `# TODO: INFERRED - needs review` when you infer behavior not explicitly in code
   - Use `# TODO: AMBIGUOUS in code` when logic is unclear
   - Use `# NOTE: Multiple implementations found` for inconsistencies

6. **Flag architectural issues:**
   - Identify tight coupling, missing transaction boundaries
   - Note duplicated logic across components
   - Suggest potential improvements
   - Format as: `# ARCHITECTURAL OBSERVATIONS:`

ANALYSIS STEPS:

Step 1: Identify the domain model
- Find all entities/models
- Extract fields, types, relationships
- Identify enums and constants
- Map database constraints to HUSL constraints

Step 2: Analyze operations
- Find API endpoints or public methods
- Extract input parameters and output types
- Identify preconditions from validation code
- Map business logic to effects
- Extract error handling to error cases

Step 3: Detect patterns
- Look for status/state fields → state machines
- Find authorization checks → rules
- Identify cross-cutting concerns (logging, notifications)
- Detect background jobs (cron, scheduled tasks)

Step 4: Extract tests
- Convert test code to given/when/then format
- Group by operation and scenario
- Include representative samples

Step 5: Document service boundaries (if distributed system)
- Identify external service calls
- Map event publishers/subscribers
- Document dependencies

OUTPUT FORMAT:

Generate a complete HUSL markdown document with all standard sections. Include an "Extraction Notes" section at the end:

---

## Extraction Notes (for human review)

**Confidence:** [high | medium | low]

**Extraction Summary:**
- Entities extracted: [count]
- Operations extracted: [count]
- Tests included: [X] of [Y] (representative sample)
- Business rules identified: [count]

**Summarized sections:**
- [Section]: [What was summarized and why]

**Requires human review:**
- [Item 1]: [Why it needs review]
- [Item 2]: [Why it needs review]

**Architectural observations:**
- [Observation 1]
- [Observation 2]

**Suggested improvements:**
- [Suggestion 1]
- [Suggestion 2]

**Inconsistencies found:**
- [Inconsistency 1]
- [Inconsistency 2]

---

IMPORTANT REMINDERS:
- Focus on WHAT the system does (business logic), not HOW it's implemented
- Use HUSL conventions (PascalCase for entities, camelCase for fields, etc.)
- Mark any uncertainty clearly
- The spec should be complete enough to regenerate working code
- Include enough detail that a new team member could understand the system

BEGIN EXTRACTION:
```

### Usage Example

```
You are a senior software architect extracting a Human-Understandable Specification Language (HUSL) specification from an existing codebase.

[... paste full prompt template above ...]

INPUTS PROVIDED:
- Programming language: Java
- Framework: Spring Boot 3.2.0
- Code files:

[Paste DomainService.java]
[Paste DomainController.java]
[Paste Domain.java]
[Paste DomainServiceTest.java]

Stack configuration:
```yaml
language: {name: java, version: "17"}
framework: {name: spring-boot, version: "3.2.0"}
api: {style: rest}
```

BEGIN EXTRACTION:
```