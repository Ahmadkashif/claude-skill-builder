# Code Quality via *Accelerate*

**Essence:** High delivery performance and code quality reinforce each other. Fast feedback, trunk-based development, and automated checks keep code healthy.

## Core practices
- **Small batch size:** Keep PRs small and integrate daily to reduce merge risk.
- **Fast, reliable pipelines:** Deterministic builds, parallel tests, and caching to keep feedback under minutes.
- **Continuous review:** Pairing or short-lived review loops to catch quality issues early.
- **Guardrails over gates:** Feature flags and canaries reduce fear of frequent deploys.

## Repository enforcement techniques
- Require green CI (lint, unit tests) before merge; surface flakiness quickly.
- Enforce branch protection with status checks; encourage trunk-based branching.
- Add deployment checklists with rollback steps tied to metrics.

## Zero-shot prompts you can reuse
- “Given this PR diff, propose smaller, independently deployable slices.”
- “Identify missing automated checks for this service (lint, unit, contract, security).”
- “Suggest flag strategy to release this change safely and roll back fast.”

## Example flow
1) Break a large refactor into three PRs behind a flag.  
2) Ensure CI runs lint + tests in parallel; fail on flake detection.  
3) Use the flag strategy prompt to define rollout and rollback.  
Result: cleaner merges, faster feedback, and safer releases that preserve code quality.
