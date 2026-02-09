# Code Quality via *The Pragmatic Programmer*

**Essence:** Ship adaptable code by keeping it DRY, orthogonal, and well-communicated. Small, reversible changes and good tooling reduce entropy.

## Core practices
- **DRY & orthogonality:** Factor shared logic; avoid hidden couplings. Example: extract shared validation into a helper and reuse it across services.
- **Tracer bullets:** Deliver thin vertical slices to validate direction early; refine based on feedback instead of big-bang rewrites.
- **Automation first:** Script repetitive checks (format, lint, tests) to keep main branch clean.
- **Contracts & assertions:** Make assumptions explicit with pre/postconditions and fast fails.

## Repository enforcement techniques
- Add formatters/linters to CI; block merges on failures.
- Use ADRs for notable design choices to prevent architectural drift.
- Keep checklists for code reviews (naming, error handling, logging discipline).

## Zero-shot prompts you can reuse
- “Review this diff for DRY violations or hidden couplings. Propose extractions or interface boundaries.”
- “Find places where intent is unclear; suggest renames or guard clauses that express invariants.”
- “Given this module, list assumptions and turn them into assertions/tests.”

## Example flow
1) Open a PR that adds a new endpoint.  
2) Run formatter + lint + tests locally (or pre-commit).  
3) Use the DRY prompt above; if duplication is found, extract helpers.  
4) Add a brief ADR entry if the endpoint changes a contract.  
Result: small, reversible change with explicit decisions and safeguards.
