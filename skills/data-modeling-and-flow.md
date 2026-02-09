# Data Modeling and Flow

**What it is:** Building on *Designing Data-Intensive Applications*, 10X engineers model data so it is consistent, evolvable, and observable. They understand how storage engines, indexes, caches, and streams shape behavior, and they pick schemas and access patterns that fit real workloads.

**Examples in practice**
- Move from ad hoc JSON blobs to versioned, validated schemas that enable backward compatibility during migrations.
- Introduce read models or caches to decouple hot paths from write-heavy domains while protecting invariants.
- Design streaming pipelines with checkpoints and replay to deliver exactly-once semantics where needed.

**Book references**
- Andrew Hunt and David Thomas, *The Pragmatic Programmer*.
- Robert C. Martin, *Clean Code*.
- Nicole Forsgren, Jez Humble, and Gene Kim, *Accelerate*.
- Martin Kleppmann, *Designing Data-Intensive Applications*.
- Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy (eds.), *Site Reliability Engineering*.
