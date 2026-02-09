# Code Quality via *Designing Data-Intensive Applications*

**Essence:** Quality code respects data correctness, evolution, and operability. Interfaces, schemas, and failure modes must be explicit and testable. (Chs: Data Models, Encodings/Evolution, Consistency/Replication, Streams)

## Core practices with examples
- **Explicit schemas:** Versioned contracts; prefer additive changes (new fields, defaults) to avoid breaking consumers.  
  Example: add `customer_tier` with default “standard”; keep old `tier` for one release; publish schema changelog.
- **Idempotence & retries:** Design handlers to tolerate duplicates and partial failures.  
  Example: use idempotency keys on `order_id`; upsert instead of insert; guard against double-charges.
- **Deterministic data flows:** Validate inputs early; ensure ordering and consistency expectations are documented.  
  Example: enforce monotonic `event_time`; drop/park events arriving out-of-order beyond a threshold; record in DLQ.
- **Observability for data paths:** Instrument checkpoints, dead-letter queues, and metrics for lag/error rates.  
  Example: emit metrics for consumer lag, DLQ counts, replay success; trace IDs through producer → broker → consumer.

## Repository enforcement techniques
- Require schema migrations with backward/forward compatibility notes and rollout steps.
- Add contract tests for producers/consumers; fail CI if breaking changes are detected.
- Include runbooks for replay/backfill to keep data integrity; store example payloads and fixtures in-repo.
- Add lint checks for idempotency markers on handlers touching external systems.

## Zero-shot prompts you can reuse
- “Review this schema change for backward/forward compatibility; propose safer alternatives.”
- “Given this event handler, make it idempotent and add guardrails for retries/timeouts.”
- “Generate contract tests to verify producer/consumer expectations for this message format.”

## Example flow (schema evolution)
1) Changing an event: add new field with default; keep old field until consumers migrate.  
2) Use compatibility prompt to ensure no breaking change; publish migration notes.  
3) Add contract tests and idempotent handling; log checkpoints for replay and DLQ.  
4) Run a backfill with replay script; monitor DLQ and lag; remove old field after confirmation.  
Result: higher data correctness with evolvable, observable code.
