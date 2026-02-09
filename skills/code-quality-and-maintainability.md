# Code Quality and Maintainability

**What it is:** Inspired by *Clean Code* and *The Pragmatic Programmer*, senior engineers create code that is easy to read, test, and change. They enforce clear boundaries, remove ambiguity, and invest in automation that keeps quality high without slowing delivery.

**Examples in practice**
- Replace large classes with cohesive modules and pure functions, increasing test coverage while shrinking bug rates. Example: split a 500-line service into `validator`, `mapper`, and `persister` modules with table-driven tests.
- Introduce fast, reliable linters and contract tests that block regressions before merge. Example: add contract tests for API responses; run `ruff`/`eslint` in CI with <10 min budget.
- Refactor risky areas incrementally behind feature flags, keeping deploys small and reversible per *Accelerate* practices. Example: gate a new calculation behind `ENABLE_NEW_TAX` flag, canary to 5%, roll back on SLO burn.

**Book references**
- Andrew Hunt and David Thomas, *The Pragmatic Programmer*.
- Robert C. Martin, *Clean Code*.
- Nicole Forsgren, Jez Humble, and Gene Kim, *Accelerate*.
- Martin Kleppmann, *Designing Data-Intensive Applications*.
- Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy (eds.), *Site Reliability Engineering*.
