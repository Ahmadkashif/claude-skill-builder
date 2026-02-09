# Code Quality via *Site Reliability Engineering*

**Essence:** Code quality includes operability. Design for reliability budgets, graceful degradation, and fast recovery to keep user-impact low.

## Core practices
- **Error budgets & SLOs:** Write code that meets latency/availability goals; prioritize fixes when budgets burn.
- **Defense in depth:** Timeouts, retries with jitter, and circuit breakers to contain failures.
- **Runbook-driven design:** Instrumentation, structured logs, and clear ownership baked into code paths.
- **Blameless learning:** Postmortems drive code hardening (tests, alerts, feature flags).

## Repository enforcement techniques
- Add SLO checks and alert routes per service; include them in PR templates.
- Mandate timeouts and cancellation in network calls; lint for missing guards.
- Keep runbooks/versioned configs alongside code; update after incidents.

## Zero-shot prompts you can reuse
- “Review this diff for missing timeouts, retries, or circuit breakers; propose safe defaults.”
- “Add structured logging/tracing to connect user symptoms to this code path.”
- “Create a minimal runbook entry for this feature: detection, mitigation, rollback.”

## Example flow
1) New outbound API call: wrap with timeout, retry-with-jitter, and circuit breaker.  
2) Use prompts to add tracing/logging and a runbook entry.  
3) Add an alert tied to SLO burn rate for that dependency.  
Result: code that is reliable, observable, and easy to operate.
