## Code Generation (HUSL → Code)

### Purpose
Generate complete, working code from a HUSL specification.

### Prompt Template

```
You are an expert code generator creating production-quality code from a Human-Understandable Specification Language (HUSL) specification.

CONTEXT:
You will generate complete, working code that implements the business logic defined in a HUSL specification. The code should be clean, well-tested, and follow the conventions specified in the stack configuration.

INPUTS PROVIDED:

HUSL Specification:
[Paste the complete .husl.md file]

Stack Configuration (stack.yml):
[Paste stack.yml]

Code Standards (standards.yml):
[Paste standards.yml]

TASK:
Generate complete, production-ready code that implements this specification.

GENERATION REQUIREMENTS:

1. **Complete implementation:**
   - All entities/models with proper annotations
   - All enums
   - All service methods implementing operations
   - All controllers/endpoints
   - All validation logic
   - All error handling
   - Complete test suite

2. **Follow the spec exactly:**
   - Every field in Schema section → model field
   - Every operation → service method + endpoint
   - Every precondition → validation check
   - Every effect → implementation logic
   - Every error case → exception or error response
   - Every test → unit test

3. **Apply stack conventions:**
   - Use framework idioms (e.g., Spring annotations for Java)
   - Follow package structure from standards.yml
   - Use naming conventions specified
   - Apply architectural patterns (layered, hexagonal, etc.)

4. **Generate tests:**
   - Convert given/when/then tests to framework test format
   - Include all test cases from spec
   - Add setup/teardown as needed
   - Use mocking library specified in stack

5. **Handle cross-cutting concerns:**
   - Implement audit logging as specified
   - Add error handling middleware
   - Include validation annotations
   - Generate appropriate exceptions

6. **State machines:**
   - Implement state transitions as specified
   - Add state validation in operations
   - Prevent invalid state transitions

7. **Background jobs:**
   - Implement scheduled tasks
   - Use framework scheduling (e.g., @Scheduled in Spring)
   - Include error handling and retry logic

CODE STRUCTURE TO GENERATE:

1. **Models/Entities:**
   - One file per entity from Schema section
   - Include all fields with types
   - Add validation annotations
   - Include constructors, getters, setters
   - Add any computed fields as methods

2. **Enums:**
   - One file per enum
   - Include all values from spec

3. **Services:**
   - Business logic layer
   - One service per domain entity
   - Implement all operations
   - Include authorization checks
   - Handle state transitions

4. **Controllers/Endpoints:**
   - API layer
   - Map operations to HTTP endpoints
   - Request/response DTOs
   - Error handling

5. **Exceptions:**
   - Custom exception classes
   - Error response models
   - Exception handlers

6. **Tests:**
   - Unit tests for services
   - Integration tests for controllers
   - Test all scenarios from spec

7. **Configuration:**
   - Application configuration
   - Scheduled task configuration (if background jobs)
   - Database configuration (if applicable)

8. **Build file:**
   - Maven pom.xml or Gradle build.gradle
   - Include all dependencies from stack.yml

QUALITY STANDARDS:

- Clean, readable code with appropriate comments
- Proper error handling (no swallowed exceptions)
- Validation at API boundary
- Authorization checks before business logic
- Idempotent operations where specified
- Transaction boundaries for data modifications
- Logging at appropriate levels

OUTPUT FORMAT:

For each file, provide:

```
// ============================================================================
// [FILE PURPOSE]
// ============================================================================

// File: [path/to/file]

[Complete file contents]
```

Start with package structure overview, then generate all files.

REMINDERS:
- Every requirement in the spec must be implemented
- Tests must cover all test cases from spec
- Follow stack and standards configurations exactly
- Code should compile and tests should pass
- Include TODOs only for infrastructure not in spec (e.g., database connections)

BEGIN GENERATION:
```

### Usage Example

```
You are an expert code generator creating production-quality code from a HUSL specification.

[... paste full prompt ...]

INPUTS PROVIDED:

HUSL Specification:
[Paste domain-crud.husl.md]

Stack Configuration:
```yaml
language: {name: java, version: "17"}
framework: {name: spring-boot, version: "3.2.0"}
build: {tool: maven}
database: {type: postgresql, orm: jpa}
testing: {unit: junit5, mocking: mockito}
```

Standards:
```yaml
project: {groupId: com.example, artifactId: domain-service}
packages: {models: models, services: services, controllers: controllers}
naming: {services: Service, controllers: Controller}
```

BEGIN GENERATION:
```
