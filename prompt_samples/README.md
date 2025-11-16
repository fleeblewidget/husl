# HUSL LLM Prompts

A collection of prompts for working with Human-Understandable Specification Language (HUSL) documents using Large Language Models.

---

## Notes on Using These Prompts

### General Tips:

1. **Start with the full language definition:** Most prompts reference or include the HUSL language definition. Have it ready to paste.

2. **Provide complete context:** The more context you give (complete specs, stack configs, etc.), the better the output.

3. **Iterate:** These are starting points. You'll often refine the output through follow-up prompts.

4. **Validate output:** Always review generated specs, code, or analyses. LLMs can make mistakes.

5. **Customize:** Adapt these prompts to your specific needs, tech stack, and conventions.

### Prompt Chaining:

For complex tasks, chain prompts:
1. Extract spec from code
2. Review extracted spec
3. Refine spec based on review
4. Generate code from refined spec
5. Review generated code

### Version Control:

- Keep prompts in version control
- Track which prompt version generated which output
- Evolve prompts based on results

### Quality Checks:

After using generation prompts, always:
- Compile/run generated code
- Run generated tests
- Review for business logic correctness
- Check for security issues

---

## Extending These Prompts

Feel free to create additional prompts for:
- Database schema generation
- API documentation generation
- Deployment script generation
- Monitoring dashboard configuration
- Security audit checklists
- Performance test scenarios

The pattern is consistent:
1. Clear context about what you're doing
2. Complete inputs (spec, config, etc.)
3. Detailed requirements for output
4. Guidelines for quality
5. Structured output format

