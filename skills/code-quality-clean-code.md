# Code Quality via *Clean Code*

**Essence:** Readability beats cleverness. Functions should be small, cohesive, and intention-revealing; classes should follow single responsibility.

## Core practices
- **Naming with intent:** Use domain terms; avoid abbreviations. Example: rename `procData()` to `normalizeOrderTotals()`.
- **Small functions:** One level of abstraction per function; extract guard clauses for error cases.
- **Meaningful tests:** Given/When/Then tests that document behavior and prevent regressions.
- **Eliminate comments-as-apologies:** Refactor code instead of explaining around it.

## Repository enforcement techniques
- Enforce style with formatter + lint rules (max complexity, max function length).
- Require tests for new paths; reject PRs increasing cyclomatic complexity without justification.
- Use pairing or mob reviews on risky refactors to keep intent intact.

## Zero-shot prompts you can reuse
- “Rewrite this function to a single responsibility with intention-revealing names. Show the diff.”
- “Identify magic numbers/booleans and replace with named constants or enums.”
- “Generate table-driven tests for these inputs/outputs; highlight missing edge cases.”

## Example flow
1) Identify a 80-line controller method.  
2) Run the single-responsibility prompt; extract sub-functions per branch.  
3) Add table-driven tests for edge cases; ensure linter complexity passes.  
4) Land change behind a flag if behavior is risky.  
Result: smaller, testable surface with clearer intent.
