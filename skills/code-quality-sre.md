# Code Quality via *Site Reliability Engineering*

**Essence:** Code quality includes operability. Design for reliability budgets, graceful degradation, and fast recovery to keep user-impact low. (Topics: SLOs/error budgets, graceful degradation, incident playbooks)

## Core practices with examples
- **Error budgets & SLOs:** Write code that meets latency/availability goals; prioritize fixes when budgets burn.  
  Example: set p99 latency SLO of 300ms; if burn >2%/day, freeze features and fix.
- **Defense in depth:** Timeouts, retries with jitter, and circuit breakers to contain failures.  
  Example: outbound call wrapped with 800ms timeout, 2 retries with jitter, circuit breaker after 5 failures/30s.
- **Graceful degradation:** Serve cached or partial results when dependencies fail.  
  Example: if recommendations API fails, return static fallbacks and log degradation.
- **Runbook-driven design:** Instrumentation, structured logs, and clear ownership baked into code paths.  
  Example: add trace IDs, structured fields (`order_id`, `user_id`); log at WARN on retries; document escalation path.
- **Blameless learning:** Postmortems drive code hardening (tests, alerts, feature flags).  
  Example: after incident, add regression test + alert + flag to disable new path.

## Repository enforcement techniques
- Add SLO checks and alert routes per service; include them in PR templates.
- Mandate timeouts and cancellation in network calls; lint for missing guards.
- Keep runbooks/versioned configs alongside code; update after incidents.
- Require fallback paths or flags for new external dependencies; add chaos tests where possible.

## Zero-shot prompts you can reuse
- “Review this diff for missing timeouts, retries, or circuit breakers; propose safe defaults.”
- “Add structured logging/tracing to connect user symptoms to this code path.”
- “Create a minimal runbook entry for this feature: detection, mitigation, rollback.”

## Example flow (new dependency)
1) New outbound API call: wrap with timeout, retry-with-jitter, and circuit breaker.  
2) Add fallback response if dependency fails; emit structured logs + trace spans.  
3) Write runbook entry (detection, mitigation, rollback); add SLO burn alert for dependency.  
Result: code that is reliable, observable, and easy to operate.
