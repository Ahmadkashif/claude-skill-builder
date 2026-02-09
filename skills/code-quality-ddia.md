# Code Quality via *Designing Data-Intensive Applications* (Deep Guide)

## What this skill is (extended overview, ~1000+ words)

In *Designing Data-Intensive Applications* (DDIA), Martin Kleppmann defines quality through the lens of data correctness, evolution, and operability. A “quality” system is one where data remains trustworthy across failures, scale, and change. That quality is expressed in code through explicit schemas, clear consistency guarantees, and resilient data pipelines. DDIA reminds us that the hardest bugs are not syntax errors but subtle data correctness failures: duplicated events, out-of-order updates, inconsistent reads, and broken assumptions between producers and consumers. The core skill, therefore, is the ability to design code that makes those assumptions explicit, testable, and observable.

DDIA starts with data models and the idea that structure determines clarity. When you choose relational, document, or graph models, you choose which relationships are easy to express and which are awkward. Code quality in this context means aligning data models with the domain so that the code “fits” the shape of the data. Schema design is not a one-time decision; it is a living contract that must evolve safely. A clean data contract uses versioning, defaults, and additive changes to keep old clients working. That means code must be written with evolution in mind, even on day one: when you add a field, what happens to old consumers? When you rename a key, how is migration handled? DDIA pushes you to encode those answers in code, tests, and documentation.

Correctness in data systems is also shaped by consistency models. DDIA explains that linearizability, causal consistency, and eventual consistency are not just academic terms; they are trade-offs that determine what your users can safely assume. Code quality requires that you pick a model and then ensure your code obeys it. If you promise read-your-writes, you must be careful with caches and replicas. If your system is eventually consistent, you must handle conflicts deterministically and make staleness explicit to users. Quality in this context means not only writing correct algorithms, but also communicating the expected behavior through API semantics, documentation, and tests.

Reliability is another pillar. DDIA shows how distributed systems fail in messy ways: network partitions, partial writes, duplicated messages, and latency spikes. Clean data code is defensive. It uses idempotency keys, deduplication logic, and retry policies with backoff. It validates inputs early and logs failures in a structured way so operators can diagnose problems. Instead of trusting the “happy path,” it treats failure as normal. That mindset is a quality skill because it leads to code that is resilient in production rather than merely correct in unit tests.

Scalability is the next dimension. DDIA emphasizes that scaling is not just about performance; it is about preserving correctness while traffic grows. Sharding, replication, and partitioning introduce new failure modes and new responsibilities in code. Quality code manages those responsibilities explicitly: it uses deterministic partition keys, guards against hot partitions, and keeps migrations backward-compatible. It also makes performance assumptions explicit by tracking latencies and queue depth. When engineers treat performance and correctness together, the system remains predictable under load, and that predictability is a hallmark of quality.

DDIA’s chapters on encoding and evolution highlight how data crosses boundaries between services, teams, and versions. Schemas are contracts, and breaking them breaks trust. Code quality is therefore about compatibility discipline: using versioned schemas, ensuring consumers can handle unknown fields, and planning multi-step migrations. It is also about tooling: schema registries, contract tests, and validation libraries that guarantee correctness in CI before data hits production. These practices prevent the class of bugs where “nothing changed” in a service but all downstream consumers suddenly fail.

Data pipelines and streaming systems introduce another layer. In streaming architectures, time is tricky: events arrive out of order, duplicates are common, and windows are approximate. Quality code makes these behaviors explicit through monotonic timestamps, watermarking, and careful use of “exactly-once” semantics. It provides dead-letter queues for poison messages and replay mechanisms with idempotent handlers. DDIA teaches that the goal is not perfect data, but explainable data: when something goes wrong, you can trace where it happened, reprocess safely, and recover without corrupting state.

DDIA also highlights transactions and the limits of distributed consistency. When you choose to use a transaction boundary, you are declaring which invariants must hold together, and when you avoid cross-service transactions, you are accepting eventual consistency and compensating actions instead. Quality code makes those trade-offs explicit through tests, documentation, and user-facing semantics. It also accounts for the reality that clocks skew and replicas diverge: using logical timestamps, version vectors, or explicit ordering rules where needed. By being clear about invariants, you avoid the hidden “it depends” behavior that causes subtle bugs.

Materialized views, caches, and derived data are another source of quality risks. DDIA argues that derived data must be reproducible and traceable, so you can rebuild it when upstream corrections occur. Quality code captures the lineage of derived data, separates state from derived views, and provides backfill tooling. This also affects performance: caches must be consistent enough for the product’s correctness needs, and cache invalidation strategies should be documented and tested. When derived data is treated as a first-class system with its own contracts, it stays reliable as the system scales.

Finally, DDIA reinforces that data quality is an operational concern. Good code instruments data paths with metrics like consumer lag, replication delay, and error rates. It logs schema versions and correlation IDs. It exposes health checks and runbooks for backfills and replay. Without observability, subtle data issues remain hidden until business outcomes degrade. Thus, the skill of code quality in DDIA is the ability to build systems where data correctness is continuously measured, not merely assumed.

Seen this way, DDIA’s code quality is about preserving truth over time. It is the discipline of writing code that treats data as a contract: versioned, validated, and observable. It is the ability to design for evolution and failure, using idempotency, explicit schemas, and compatibility strategies to keep systems dependable. When you apply these ideas, you end up with code that is easier to change without data loss, and a system that you can trust even as it scales. That trust is the practical outcome of quality in data-intensive applications.

---

## 25 To Do / Not To Do pairs with detailed explanations

**1) To Do:** Version schemas and add fields in an additive way.  
**Not To Do:** Make breaking schema changes in place.  
**Why (detailed):** Additive changes allow old consumers to continue working while they migrate. Breaking changes silently corrupt data or cause downstream failures.

**2) To Do:** Use explicit defaults for new fields.  
**Not To Do:** Assume missing fields mean “null” without documentation.  
**Why (detailed):** Defaults preserve behavior for older data and clarify intent. Undocumented nulls lead to inconsistent interpretations across services.

**3) To Do:** Make event handlers idempotent.  
**Not To Do:** Assume each event is delivered exactly once.  
**Why (detailed):** Distributed systems often deliver duplicates; idempotency prevents double writes or double charges. Non-idempotent handlers create subtle data corruption.

**4) To Do:** Use deterministic partition keys and document them.  
**Not To Do:** Pick partition keys ad hoc per feature.  
**Why (detailed):** Stable partitioning avoids hot spots and makes scaling predictable. Ad hoc keys cause uneven load and complicated migrations.

**5) To Do:** Validate inputs at the boundary with schemas.  
**Not To Do:** Accept untyped payloads and validate deep in the code path.  
**Why (detailed):** Early validation prevents bad data from flowing downstream. Late validation makes failures harder to trace and recover.

**6) To Do:** Use correlation IDs and log schema versions.  
**Not To Do:** Log only free-form strings without data context.  
**Why (detailed):** Structured logs make it possible to trace issues across pipelines. Unstructured logs obscure where corruption occurred.

**7) To Do:** Keep compatibility tests in CI for producers and consumers.  
**Not To Do:** Rely on manual coordination between teams.  
**Why (detailed):** Contract tests catch breaking changes early and consistently. Manual coordination fails under time pressure and scales poorly.

**8) To Do:** Explicitly choose a consistency model and document it.  
**Not To Do:** Promise “strong consistency” without implementing it.  
**Why (detailed):** Correctness depends on the consistency model; clients need to know what to expect. False promises create user-visible anomalies and bugs.

**9) To Do:** Use retries with exponential backoff and jitter.  
**Not To Do:** Retry in tight loops that amplify load.  
**Why (detailed):** Backoff prevents retry storms and protects availability. Tight loops amplify failures and worsen outages.

**10) To Do:** Make conflict resolution deterministic.  
**Not To Do:** Let last-write-wins happen accidentally.  
**Why (detailed):** Deterministic rules preserve data invariants. Accidental conflict behavior causes surprising data loss.

**11) To Do:** Capture out-of-order events with watermarks or buffers.  
**Not To Do:** Assume events always arrive in order.  
**Why (detailed):** Out-of-order delivery is normal in distributed systems. Ignoring it creates inconsistent aggregates and missed updates.

**12) To Do:** Provide a dead-letter queue for poison messages.  
**Not To Do:** Drop bad messages silently.  
**Why (detailed):** DLQs preserve data for later inspection and replay. Silent drops hide issues and cause permanent data loss.

**13) To Do:** Implement safe replay/backfill tooling.  
**Not To Do:** Re-run pipelines manually without idempotency checks.  
**Why (detailed):** Safe replay prevents duplicates and keeps corrections controlled. Manual replays often introduce new inconsistencies.

**14) To Do:** Track consumer lag and replication delay metrics.  
**Not To Do:** Assume data freshness without measurement.  
**Why (detailed):** Lag metrics reveal backlogs and scaling issues early. Without them, stale data problems go unnoticed.

**15) To Do:** Use write-ahead logs or transaction boundaries for critical updates.  
**Not To Do:** Write partially without rollback protection.  
**Why (detailed):** Transactional boundaries protect invariants during failures. Partial writes create inconsistent state that is hard to recover.

**16) To Do:** Prefer backward-compatible migrations (expand/contract).  
**Not To Do:** Rename or delete fields immediately.  
**Why (detailed):** Expand/contract allows gradual migration with safe rollback. Immediate changes break consumers and reduce reliability.

**17) To Do:** Encode invariants in tests and schema validations.  
**Not To Do:** Rely on business logic alone to keep data clean.  
**Why (detailed):** Tests and validations enforce correctness consistently. Business logic alone is easy to bypass or forget in new code.

**18) To Do:** Design APIs that surface staleness or conflicts.  
**Not To Do:** Pretend eventual consistency is always real-time.  
**Why (detailed):** Exposing staleness helps users make informed decisions. Hiding it leads to confusion and incorrect assumptions.

**19) To Do:** Normalize time handling with explicit time zones and clocks.  
**Not To Do:** Mix local times or implicit time zones.  
**Why (detailed):** Explicit time handling prevents ordering errors and reporting bugs. Implicit time zones create subtle inconsistencies.

**20) To Do:** Keep data processing steps small and composable.  
**Not To Do:** Build monolithic pipelines that are hard to debug.  
**Why (detailed):** Small steps are easier to test and re-run. Monolith pipelines hide errors and slow recovery.

**21) To Do:** Use checksums or hashes for data integrity in transit.  
**Not To Do:** Assume network transport is always lossless.  
**Why (detailed):** Integrity checks catch corruption early. Blind trust in transport can allow silent data damage.

**22) To Do:** Guard against hot partitions with load-aware keys.  
**Not To Do:** Use keys with skewed distribution without monitoring.  
**Why (detailed):** Hot partitions create uneven performance and failures. Load-aware design keeps throughput predictable.

**23) To Do:** Use schema registries or central docs for data contracts.  
**Not To Do:** Let each service document contracts independently.  
**Why (detailed):** Centralized contracts make compatibility visible and enforceable. Scattered documentation leads to drift and broken assumptions.

**24) To Do:** Capture data lineage and provenance in logs or metadata.  
**Not To Do:** Lose track of where data came from.  
**Why (detailed):** Lineage helps debug anomalies and recover from errors. Without it, root-cause analysis is guesswork.

**25) To Do:** Treat data correctness as an SLO with alerts.  
**Not To Do:** Detect data issues only through user complaints.  
**Why (detailed):** Data-quality alerts surface issues before they cascade. Reactive detection increases customer impact and recovery time.

---

## How to apply this in your repo (concise checklist)
- Maintain versioned schemas and require compatibility notes in PRs.
- Add contract tests and schema validation in CI for producers/consumers.
- Make handlers idempotent; document retry and backfill behavior.
- Instrument data pipelines with lag, error rate, and DLQ metrics.
- Use expand/contract migrations and keep rollback steps documented.
- Standardize time handling, partition keys, and data lineage metadata.
- Treat data correctness as a measurable SLO with alerting.
