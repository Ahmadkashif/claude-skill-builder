# Code Quality via *Accelerate* (Deep Guide)

## What this skill is (extended overview, ~1000+ words)

High-performing engineering teams ship reliable changes quickly because they bake code quality into the delivery system itself. *Accelerate* (Forsgren, Humble, Kim) demonstrates through data that elite performers achieve both speed and stability by investing in fast feedback, small batch sizes, continuous testing, trunk-based development, loosely coupled architectures, and progressive delivery. Code quality here is not just readable code—it is the ability to change code confidently. That confidence emerges from guardrails (tests, linters, contracts), observability (metrics, traces, logs), and working habits (pairing, review discipline, and operational readiness) that reduce batch size, shorten lead time, and cut change-failure rate (CFR).

The “quality as a system property” mindset reframes individual best practices into system feedback loops:

- **Fast feedback** (builds/tests under 10–15 minutes) turns mistakes into cheap learnings; long pipelines convert small mistakes into late disasters.
- **Small batches** limit blast radius, reduce merge conflicts, and make root-cause analysis trivial. Large batches hide defects and create painful releases.
- **Continuous testing and automation** free engineers to focus on intent and design, not manual checks. Unit, integration, contract, and smoke tests run on every change.
- **Trunk-based development** minimizes long-lived branches, enabling frequent integration and avoiding “merge hell.”
- **Progressive delivery** (feature flags, canaries, blue/green) decouples deploy from release, so you can ship often without risking all users.
- **Loose coupling and good boundaries** let teams deploy independently; tight coupling creates lockstep releases and blocks flow.
- **Observability and SLOs** give runtime feedback; without them, you cannot tell if a change is healthy until users complain.
- **Culture of continual improvement** (retros, blameless postmortems) turns incidents and flakes into fuel for better quality.

From a code perspective, the skill encompasses:

1. **Designing for deployability:** components are testable, observable, parameterized, and flaggable.
2. **Designing for diagnosability:** structured logs, traces, metrics wired to user-facing paths; errors contain actionable context.
3. **Designing for reversibility:** flags, rollbacks, migrations with backstops; config- and data-gated rollouts.
4. **Designing for verification:** contracts between services, schema evolution plans, deterministic tests with hermetic fixtures.

Practically, an engineer applying *Accelerate* will:

- Keep PRs small (ideally <300 lines net) and integrate daily; large refactors are sliced behind flags.
- Maintain a pipeline budget (e.g., <10 minutes) and constantly deflake. Flaky tests are treated as incidents.
- Ensure every change runs linters, unit tests, contract tests, and smoke tests in CI; security and dependency checks are part of the same pipeline.
- Use feature flags to separate deploy from release, and define rollout/rollback playbooks before merging.
- Track DORA metrics (lead time, deploy frequency, change-fail rate, MTTR) and use them to prioritize pipeline and quality work.
- Write runbooks and add SLOs/alerts for new dependencies; measure error budgets and pause feature work when burning.

Why this matters: Code quality divorced from delivery speed is fragile—either you ship slowly or you ship broken things. The *Accelerate* model shows that quality and speed reinforce each other when feedback loops are tight. The practices below are expressed as To Do / Not To Do pairs so you can operationalize them in your repository. Each pair is followed by a detailed explanation to illustrate the “why” with concrete, code- and pipeline-level behaviors.

---

## 25 To Do / Not To Do pairs with detailed explanations

**1) To Do:** Keep PRs small and integrate daily.  
**Not To Do:** Accumulate week-long branches with massive diffs.  
**Why (detailed):** Small PRs limit cognitive load for reviewers, reduce merge conflicts, and localize defects. Daily integration keeps trunk stable and aligns with trunk-based development. Large branches diverge, force painful rebases, and mask defects that surface only at integration time.

**2) To Do:** Enforce a pipeline budget (e.g., <10–15 minutes) with parallelized lint, unit, contract, and smoke tests.  
**Not To Do:** Allow pipelines to drift to 30–60 minutes with serial steps.  
**Why:** Fast feedback lets engineers fix defects while context is fresh. Parallelization (e.g., sharding tests, caching deps) keeps quality gates strict without slowing flow. Slow pipelines encourage bypassing checks, reduce deploy frequency, and increase batch size.

**3) To Do:** Treat flaky tests as incidents; quarantine and fix within 24–48 hours.  
**Not To Do:** Re-run flaky tests repeatedly or mark them ignored.  
**Why:** Flakes erode trust in tests, leading to skipped checks and reduced signal. Quarantining isolates risk while a fix is pursued. Persistent flakes inflate CFR because they mask real regressions and slow delivery.

**4) To Do:** Use feature flags to decouple deploy from release; design explicit rollout and rollback plans.  
**Not To Do:** Ship big changes as all-or-nothing deploys.  
**Why:** Flags enable canaries and partial rollouts, reducing blast radius. Rollback becomes a config change, not a redeploy. Without flags, any defect forces full rollback or emergency patches, lengthening MTTR.

**5) To Do:** Write contract tests between services/clients to catch breaking API/schema changes.  
**Not To Do:** Rely solely on end-to-end tests for integration safety.  
**Why:** Contract tests are faster, more precise, and run per change. E2E-only strategies are brittle and slow, delaying feedback and hiding compatibility issues until late.

**6) To Do:** Keep dependencies updated with automated checks and vulnerability scanning.  
**Not To Do:** Pin ancient versions and defer updates for months.  
**Why:** Smaller, frequent dependency bumps reduce risk and make CVE remediation routine. Large, infrequent upgrades require big-bang testing and often break builds, reducing velocity and quality.

**7) To Do:** Maintain hermetic, deterministic tests with seeded data and isolated services (mocks/fakes where appropriate).  
**Not To Do:** Depend on shared, mutable test environments.  
**Why:** Determinism reduces flakiness and enables parallelization. Shared environments introduce hidden couplings and data races, driving flakes and false positives.

**8) To Do:** Instrument code paths with structured logs, traces, and metrics tied to user-facing SLOs.  
**Not To Do:** Log ad hoc strings without context or omit telemetry.  
**Why:** Observability shortens MTTR and reduces CFR. Structured fields (user_id, request_id) make correlations possible; SLOs provide an objective standard for health and rollback decisions.

**9) To Do:** Enforce timeouts, retries with jitter, and circuit breakers on outbound calls; test them in CI.  
**Not To Do:** Make blocking calls without limits or backoff.  
**Why:** Defensive patterns prevent cascading failures and protect SLOs. Testing these behaviors (via integration or chaos tests) ensures code paths behave under failure, preserving quality in production.

**10) To Do:** Use migrations that are backward/forward compatible; roll out in stages with validation.  
**Not To Do:** Perform in-place breaking migrations.  
**Why:** Safe rollout patterns (expand/contract) keep systems operable during deploys. Breaking changes spike error budgets and prolong outages; staged migrations allow quick rollback and verification.

**11) To Do:** Track and publish DORA metrics; prioritize work that improves lead time, deploy frequency, CFR, and MTTR.  
**Not To Do:** Ignore delivery metrics and optimize locally (e.g., only for throughput).  
**Why:** DORA metrics correlate with org performance. If CFR is high, invest in tests/flags; if lead time is high, optimize pipelines. Without metrics, quality initiatives lack direction.

**12) To Do:** Gate risky code paths with runtime killswitches and safe defaults.  
**Not To Do:** Hardwire new logic with no escape hatch.  
**Why:** Killswitches reduce MTTR by allowing immediate disablement. Safe defaults prevent outages when configs are missing. Without them, remediation requires code redeploys, lengthening incidents.

**13) To Do:** Practice continuous review (pairing/mobbing on complex changes; rapid async review on small PRs).  
**Not To Do:** Allow long review queues or single-reviewer silos.  
**Why:** Fast, collaborative review catches defects early, spreads context, and keeps flow moving. Slow review increases batch size and encourages risky unreviewed merges.

**14) To Do:** Keep CI as close to prod as practical: same build steps, similar configs, smoke tests post-deploy.  
**Not To Do:** Let CI drift from production paths.  
**Why:** Drifts create false confidence; builds that pass CI but fail in prod harm trust. Smoke tests after deploy catch environment-specific issues quickly, reducing CFR.

**15) To Do:** Automate static analysis (linters, formatters, security checks) and make them non-optional.  
**Not To Do:** Run lint/security checks manually or optionally.  
**Why:** Automation enforces baseline quality and consistency. Optional checks are skipped under pressure, leading to regressions and vulnerabilities.

**16) To Do:** Design rollouts with measurable success criteria and abort conditions (SLO burn, error rates, business KPIs).  
**Not To Do:** Roll out without metrics or clear stop rules.  
**Why:** Predefined thresholds prevent debate during incidents and speed rollback. Without them, teams delay decisions, increasing impact and CFR.

**17) To Do:** Keep documentation and runbooks versioned with code; update them with each change.  
**Not To Do:** Rely on tribal knowledge or stale docs.  
**Why:** Fresh runbooks reduce MTTR and onboarding time. Stale docs cause misdiagnosis during incidents and slow resolution.

**18) To Do:** Use canary + blue/green strategies for services with high blast radius.  
**Not To Do:** Roll out high-risk changes globally first.  
**Why:** Staged exposure limits impact and provides real-world validation before full release. Global-first rollouts amplify defects and user pain.

**19) To Do:** Budget time for pipeline upkeep and deflaking every sprint.  
**Not To Do:** Defer pipeline work indefinitely in favor of features.  
**Why:** Pipeline health is a force multiplier for code quality. Neglect leads to slow feedback, higher CFR, and reduced deploy frequency.

**20) To Do:** Enforce test ownership and clear alerts for failing tests (e.g., code owners).  
**Not To Do:** Let failing tests languish without owners.  
**Why:** Clear ownership drives accountability and fast fixes. Orphaned failures normalize red builds, eroding quality culture.

**21) To Do:** Prefer dependency injection and seams that make code testable.  
**Not To Do:** Hardcode infrastructure details inside business logic.  
**Why:** Testability supports small, fast tests and reduces flakiness. Hardcoded deps force brittle integration tests and slow pipelines.

**22) To Do:** Measure and cap build artifact size/startup time; optimize regularly.  
**Not To Do:** Ignore bloat until deploys fail.  
**Why:** Bloat slows deploys and can cause timeouts during rollouts, increasing MTTR. Regular pruning maintains fast delivery and reliability.

**23) To Do:** Run post-deploy smoke tests and synthetic checks tied to SLOs.  
**Not To Do:** Assume green deploy equals healthy service.  
**Why:** Some issues manifest only in live envs (config, network, IAM). Smoke tests catch them early, preventing prolonged user impact.

**24) To Do:** Integrate incident learnings into code/tests/pipeline quickly (e.g., add regression tests, alerts, flags).  
**Not To Do:** File follow-ups without execution.  
**Why:** Closing the loop hardens systems and reduces recurrence. Unacted postmortems keep CFR and MTTR high.

**25) To Do:** Continuously simplify architecture to reduce coupling (strangle patterns, seams for legacy).  
**Not To Do:** Add new layers of indirection without paying down old complexity.  
**Why:** Lower coupling enables independent deploys and faster, safer changes. Accumulated complexity raises CFR and slows delivery.

---

## How to apply this in your repo (concise checklist)
- Keep PRs small; enforce review SLAs; integrate daily.
- Maintain <10–15 minute pipelines; parallelize tests; deflake continuously.
- Use feature flags + rollout/rollback playbooks; canary high-risk changes.
- Add contract tests, static analysis, and security checks to CI; block merges on red.
- Instrument with metrics/logs/traces; set SLOs and alerts; run smoke tests post-deploy.
- Practice trunk-based development; avoid long-lived branches.
- Treat flaky tests and failing pipelines as incidents; fix fast.
- Track DORA metrics; prioritize work that reduces CFR/MTTR and lead time.
