# Code Quality via *Designing Data-Intensive Applications*

**Essence:** Quality code respects data correctness, evolution, and operability. Interfaces, schemas, and failure modes must be explicit and testable.

## Core practices
- **Explicit schemas:** Versioned contracts; prefer additive changes (new fields, defaults) to avoid breaking consumers.
- **Idempotence & retries:** Design handlers to tolerate duplicates and partial failures.
- **Deterministic data flows:** Validate inputs early; ensure ordering and consistency expectations are documented.
- **Observability for data paths:** Instrument checkpoints, dead-letter queues, and metrics for lag/error rates.

## Repository enforcement techniques
- Require schema migrations with backward/forward compatibility notes.
- Add contract tests for producers/consumers; fail CI if breaking changes are detected.
- Include runbooks for replay/backfill to keep data integrity.

## Zero-shot prompts you can reuse
- “Review this schema change for backward/forward compatibility; propose safer alternatives.”
- “Given this event handler, make it idempotent and add guardrails for retries/timeouts.”
- “Generate contract tests to verify producer/consumer expectations for this message format.”

## Example flow
1) Changing an event: add new field with default; keep old field until consumers migrate.  
2) Use compatibility prompt to ensure no breaking change.  
3) Add contract tests and idempotent handling; log checkpoints for replay.  
Result: higher data correctness with evolvable, observable code.
