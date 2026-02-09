# System Design and Trade-offs

**What it is:** Senior 10X engineers design systems that balance correctness, cost, scalability, and changeability. Drawing on *The Pragmatic Programmer* and *Designing Data-Intensive Applications*, they decompose problems into well-bounded components, choose protocols and data models intentionally, and make trade-offs explicit so teams can evolve safely.

**Examples in practice**
- Turn a monolith with slow releases into domain-aligned services with clear contracts, guided by Kleppmann’s consistency/availability trade-offs.
- Select queues and idempotent consumers to smooth bursty traffic, explicitly documenting failure modes and retries.
- Write architectural decision records (ADRs) that capture options, trade-offs, and reasons, enabling future engineers to revisit decisions quickly.

**Book references**
- Andrew Hunt and David Thomas, *The Pragmatic Programmer*.
- Robert C. Martin, *Clean Code*.
- Nicole Forsgren, Jez Humble, and Gene Kim, *Accelerate*.
- Martin Kleppmann, *Designing Data-Intensive Applications*.
- Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy (eds.), *Site Reliability Engineering*.
