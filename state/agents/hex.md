# Hex State

This file is Hex's running state surface inside the knowledge-vault.

Use it for durable maintenance actions only, not for noisy command logs.

Preferred entry types:
- ingest
- query-backfill
- lint
- decision
- protocol
- handoff

Template:

```md
## [YYYY-MM-DD] <type> | <topic>

### What changed
- ...

### Outputs
- [[note-a]]
- [[note-b]]

### Current state
- ...

### Next
- ...

### Needs review
- ...
```
