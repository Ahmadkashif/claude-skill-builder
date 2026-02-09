# Code Quality via *Accelerate*

**Essence:** High delivery performance and code quality reinforce each other. Fast feedback, trunk-based development, and automated checks keep code healthy. (Chs: Continuous Delivery, Architecture, Technical Practices)

## Core practices with examples
- **Small batch size:** Keep PRs small and integrate daily to reduce merge risk.  
  Example: split a refactor into “extract validation,” “add tests,” and “flip flag” PRs merged daily.
- **Fast, reliable pipelines:** Deterministic builds, parallel tests, and caching to keep feedback under minutes.  
  Example: shard unit tests across 4 runners; cache deps; fail builds on flakiness detection.
- **Continuous review:** Pairing or short-lived review loops to catch quality issues early.  
  Example: 15-minute async review SLA on small PRs; pair on tricky changes.
- **Guardrails over gates:** Feature flags and canaries reduce fear of frequent deploys.  
  Example: deploy behind `enable_new_pricing`; canary to 5% traffic; auto-rollback on SLO burn.

## Repository enforcement techniques
- Require green CI (lint, unit tests) before merge; surface flakiness quickly with retry ceilings and quarantining.
- Enforce branch protection with status checks; encourage trunk-based branching and daily merges.
- Add deployment checklists with rollback steps tied to metrics; store runbooks in-repo.
- Track DORA metrics (lead time, deploy frequency, change fail rate, MTTR) to ensure practices improve quality.

## Zero-shot prompts you can reuse
- “Given this PR diff, propose smaller, independently deployable slices.”
- “Identify missing automated checks for this service (lint, unit, contract, security).”
- “Suggest flag strategy to release this change safely and roll back fast.”

## Example flow (pipeline + flag)
1) Break a large refactor into three PRs behind a flag.  
2) Parallelize lint + unit + contract tests; cap total pipeline <10 minutes.  
3) Use flag strategy prompt to define rollout (5% canary → 25% → 100%) and rollback triggers (SLO burn, error spikes).  
4) Track lead time and change fail rate; fix flakiness before new features.  
Result: cleaner merges, faster feedback, and safer releases that preserve code quality.
