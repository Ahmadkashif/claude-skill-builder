# Code Quality via *Clean Code*

**Essence:** Readability beats cleverness. Functions should be small, cohesive, and intention-revealing; classes should follow single responsibility. (Chs: Meaningful Names, Functions, Comments, Error Handling)

## Core practices with examples
- **Naming with intent:** Use domain terms; avoid abbreviations.  
  Example: rename `procData()` to `normalize_order_totals()`; rename `flag` to `is_tax_exempt`.
- **Small, single-purpose functions:** One level of abstraction per function; extract guard clauses for error cases.  
  Example: split a 80-line controller into `parse_request()`, `authorize_user()`, `build_order()`, `persist_order()`.
- **Meaningful tests:** Given/When/Then tests that document behavior and prevent regressions.  
  Example (table-driven):
  ```python
  cases = [
    ("taxable", 100, False, 108),
    ("exempt",  100, True, 100),
  ]
  ```
- **Replace comments with clarity:** Remove “// quick hack” by extracting well-named functions or constants (e.g., `SALES_TAX_RATE = 0.08`).

## Repository enforcement techniques
- Enforce style with formatter + lint rules (max complexity, max function length).
- Require tests for new paths; reject PRs increasing cyclomatic complexity without justification.
- Use pairing or mob reviews on risky refactors to keep intent intact.
- Add PR template checkboxes: “renamed for intent,” “extracted guards,” “added table-driven tests.”

## Zero-shot prompts you can reuse
- “Rewrite this function to a single responsibility with intention-revealing names. Show the diff.”
- “Identify magic numbers/booleans and replace with named constants or enums.”
- “Generate table-driven tests for these inputs/outputs; highlight missing edge cases.”

## Example flow (refactor a long function)
1) Identify an 80-line controller method.  
2) Use the single-responsibility prompt; extract guard clauses and domain steps into named helpers.  
3) Replace magic numbers with constants; add table-driven tests for edge cases.  
4) Ensure linter complexity passes; merge once tests and reviewers sign off.  
Result: smaller, testable surface with clearer intent and safer changes.
