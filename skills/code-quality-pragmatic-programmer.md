# Code Quality via *The Pragmatic Programmer*

**Essence:** Ship adaptable code by keeping it DRY, orthogonal, and well-communicated. Small, reversible changes and good tooling reduce entropy. (Chs: Pragmatic Paranoia, DRY, Orthogonality, Tracer Bullets)

## Core practices with examples
- **DRY & orthogonality:** Factor shared logic; avoid hidden couplings.  
  Example: move duplicate `validate_price()` and `validate_currency()` from three handlers into `pricing/validators.py`; import everywhere. Run linters to ensure no direct field math outside the helper.
- **Tracer bullets:** Deliver thin vertical slices to validate direction early.  
  Example: ship a minimal read-only endpoint `/orders/:id` that hits the new data store while writes stay on the old path. Measure latency and error rates before migrating writes.
- **Automation first:** Script repetitive checks (format, lint, tests) to keep main branch clean.  
  Example: add `pre-commit` hooks for `ruff`/`black` and run them in CI; block merges on failures.
- **Contracts & assertions:** Make assumptions explicit with pre/postconditions and fast fails.  
  Example: assert that `discount` is between 0–1 before computing totals; raise and log with context instead of silently clamping.

## Repository enforcement techniques
- Add formatters/linters to CI; block merges on failures. Keep configs versioned.
- Use ADRs for notable design choices (e.g., choosing GRPC vs REST) to prevent drift and record trade-offs.
- Keep checklists for code reviews (naming, error handling, logging discipline) and rotate reviewers to catch coupling.

## Zero-shot prompts you can reuse
- “Review this diff for DRY violations or hidden couplings. Propose extractions or interface boundaries.”
- “Find places where intent is unclear; suggest renames or guard clauses that express invariants.”
- “Given this module, list assumptions and turn them into assertions/tests.”

## Example flow (tracer bullet)
1) Add minimal read-only endpoint hitting the new service.  
2) Run formatter + lint + tests locally (or pre-commit).  
3) Use DRY prompt; extract shared validation to a module.  
4) Add ADR documenting the migration approach and rollback plan.  
Result: reversible thin slice that proves architecture while keeping code orthogonal.
