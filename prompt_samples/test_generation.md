## Test Generation

### Purpose
Generate comprehensive test cases from a HUSL specification.

### Prompt Template

``````
You are a QA engineer generating comprehensive test cases from a Human-Understandable Specification Language (HUSL) specification.

CONTEXT:
You need to generate a complete test suite that validates all requirements in a HUSL specification. Tests should be thorough, covering happy paths, error cases, edge cases, and boundary conditions.

HUSL SPECIFICATION:
[Paste .husl.md file]

TARGET TEST FRAMEWORK:
[JUnit 5 / pytest / Jest / etc.]

TASK:
Generate a comprehensive test suite that validates all requirements in the specification.

TEST GENERATION REQUIREMENTS:

1. **Coverage:**
   - All operations (happy path)
   - All error cases
   - All preconditions
   - All business rules
   - All state transitions
   - Authorization scenarios
   - Edge cases and boundaries

2. **Test structure:**
   - Convert given/when/then from spec to test framework format
   - Use mocking for dependencies
   - Set up test data properly
   - Clear assertions

3. **Test organization:**
   - Group by operation or feature
   - Logical test names
   - Test cases cover single concern
   - Tests are independent

4. **Quality:**
   - Tests are deterministic
   - Tests are maintainable
   - Clear failure messages
   - Appropriate use of fixtures/setup

OUTPUT FORMAT:

For each test file:

```[language]
// ============================================================================
// [TEST FILE PURPOSE]
// Tests for [Feature/Operation]
// ============================================================================

[Imports]

public class [TestClassName] {

    // Test setup
    [Setup code]

    // ========================================
    // [OPERATION NAME] - Happy Path Tests
    // ========================================

    @Test
    public void [testName]() {
        // Given (from spec)
        [Test setup matching spec given]

        // When (from spec)
        [Operation call matching spec when]

        // Then (from spec)
        [Assertions matching spec then]
    }

    // ========================================
    // [OPERATION NAME] - Error Cases
    // ========================================

    [Error case tests]

    // ========================================
    // [OPERATION NAME] - Edge Cases
    // ========================================

    [Edge case tests]

    // ========================================
    // [OPERATION NAME] - Authorization Tests
    // ========================================

    [Authorization tests]
}
```

TEST GENERATION GUIDELINES:

- Every test case from spec → test method
- Use spec test names as basis for method names
- Match spec given/when/then structure
- Add additional edge cases not in spec
- Test boundary conditions
- Test null/empty cases
- Test concurrent scenarios if relevant
- Include performance tests if SLA specified

BEGIN TEST GENERATION:
``````
