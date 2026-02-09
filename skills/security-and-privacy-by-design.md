# Security and Privacy by Design

**What it is:** Senior engineers bake security into design, coding, and operations. Informed by *The Pragmatic Programmer* and SRE practices, they threat-model early, minimize attack surface, and ensure least privilege while respecting user privacy.

**Examples in practice**
- Perform threat models for new features, adding input validation, rate limits, and encryption in transit and at rest.
- Implement least-privilege IAM roles and secret rotation; remove long-lived credentials from code and CI.
- Add security checks (static analysis, dependency scanning) to the delivery pipeline to block known vulnerabilities.

**Book references**
- Andrew Hunt and David Thomas, *The Pragmatic Programmer*.
- Robert C. Martin, *Clean Code*.
- Nicole Forsgren, Jez Humble, and Gene Kim, *Accelerate*.
- Martin Kleppmann, *Designing Data-Intensive Applications*.
- Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy (eds.), *Site Reliability Engineering*.
