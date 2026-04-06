# VAULT.md

## Positioning

This vault is not a casual note dump.

It is evolving toward an **agent-maintained long-term knowledge system**.

The goal is not merely to store notes, but to let knowledge:
- accumulate
- stay linked
- stay reviewable
- stay maintainable over time

The human is not necessarily the primary writer of every note.
The long-term target is:

> **agent as primary maintainer, human as source curator, judge, reviewer, and protocol designer.**

This vault should increasingly behave less like a pile of markdown and more like a maintained wiki.

---

## Three Logical Layers

This vault currently follows **logical three-layer separation**, even if the physical directory layout is not yet fully split.

### 1. Raw Sources
Raw materials that inform knowledge work.

Examples:
- articles
- papers
- repo docs
- source code reading targets
- transcripts
- collected references
- external notes imported into local markdown

Properties:
- source-oriented
- should not be silently rewritten into “knowledge pages” without interpretation
- may be immutable or treated as effectively immutable inputs

### 2. Wiki Layer
The maintained knowledge layer.

Examples:
- summaries
- concept notes
- synthesis notes
- decision notes
- comparison pages
- README / index pages
- topic workspaces

Properties:
- this is the compounding artifact
- should be increasingly maintained by agent workflows
- should remain linkable, structured, and reviewable

### 3. Schema Layer
The maintenance protocol layer.

Examples:
- `VAULT.md`
- governance rules
- README/index conventions
- ingest/query-backfill/lint rules
- skill-driven maintenance workflows

Properties:
- tells the agent how to maintain the vault
- separates disciplined maintenance from ad-hoc editing
- should become more explicit over time

---

## Core Operations

The vault should be maintained through three recurring operations.

## 1. Ingest
When new source material arrives, the agent should not only summarize it once.
It should consider whether the source should update the maintained knowledge layer.

Typical ingest actions:
- read the source
- extract key takeaways
- create or update a summary page
- update related topic / concept / project notes
- update README / index pages when needed
- append or preserve traceability where appropriate

Key idea:

> ingest is not “save source and move on”; it is incremental knowledge compilation.

## 2. Query-backfill
Good conversations should not die in chat history.

When a discussion produces durable value, the agent should propose writing it back into the vault.

This is currently one of the most important missing protocols.

Typical trigger cases:
- a decision gets formally made
- a repeatable workflow is defined
- a structural analysis becomes clear
- a reading discussion produces real synthesis

Key idea:

> query is not just consumption; good query output should become new knowledge assets.

## 3. Lint
The vault should be periodically health-checked.

Typical lint targets:
- duplicate truths
- stale or contradictory notes
- orphan pages
- missing README / index in dense folders
- boundary drift between Projects / Research / Reference
- root noise and stray files
- obsolete workflow directories still acting like canonical homes

Key idea:

> a useful knowledge system is not only written — it is maintained.

---

## Default Query-Backfill Types

When a conversation produces durable value, the agent should classify it into one of these default types.

## 1. Decision
A judgment that changes future default behavior.

Examples:
- “from now on, default to …”
- “retire this directory”
- “use this workflow as standard”
- “this repo becomes the canonical source of truth”

Default destination:
- project note
- topic workspace
- explicit decision note

Rule:

> decision changes future execution behavior.

## 2. Protocol
A repeatable workflow, maintenance rule, or operating discipline.

Examples:
- ingest rules
- skill authoring workflow
- canonical config location rules
- git closeout discipline

Default destination:
- governance note
- README
- workflow note
- skill / maintenance doc
- `TOOLS.md` / related protocol file when scope is personal-environment level

Rule:

> protocol changes how recurring work should be performed.

## 3. Analysis
A durable interpretation, distinction, comparison, or structural judgment that may not yet impose execution constraints.

Examples:
- comparing two approaches
- explaining why one architecture fits better
- identifying the real bottleneck in a workflow
- mapping an external theory onto current practice

Default destination:
- `03-Research/...`
- analysis note near the topic

Rule:

> analysis changes understanding, not necessarily immediate execution.

## 4. Reading Synthesis
A synthesis tied closely to reading a source.

Examples:
- co-reading takeaways
- source-reading notes
- gist/article interpretation
- “what matters here for us” notes

Default destination:
- companion reading note near the original source
- related research directory

Rule:

> reading synthesis transforms source reading into reusable local knowledge.

---

## Agent Default Behavior

Long-term direction:
- the agent should increasingly act as primary maintainer of the wiki layer
- the human should increasingly act as reviewer, judge, and protocol designer

Current practical default:

1. agent identifies possible backfill
2. agent proposes classification + destination
3. agent drafts the note / patch
4. human approves or revises when needed
5. agent writes and integrates it

This is more active than “just ask whether to save it”, but still keeps human judgment where stakes are real.

---

## Current Vault-Specific Rules

### Rule 1: Root stays clean
The vault root should not become a parking lot for stray notes or temporary artifacts.

### Rule 2: Dense folders need navigation
High-density folders should gain a `README.md` or index before continuing to sprawl.

### Rule 3: Content creation is topic-based
Canonical long-term content workspaces live under:

- `02-Projects/内容创作/<主题>/`

Each topic should prefer:
- `README.md`
- `00-source/`
- `10-wechat/`
- `20-xiaohongshu/`

### Rule 4: Channel folders are not canonical truth
`公众号创作` and `07-Posts/WeChat` are no longer canonical long-term正文 roots.

### Rule 5: Projects / Research / Reference must stay distinct
- `Projects` = active workspaces and execution contexts
- `Research` = understanding, comparison, interpretation, source reading
- `Reference` = stable facts, docs mirrors, manuals, reusable lookup material

### Rule 6: README/index work is real work
README/index creation is not cosmetic; it is part of the maintained knowledge structure.

---

## Multi-Agent State / Log

This vault assumes multi-agent collaboration is normal, not exceptional.

Therefore, a single root-level `log.md` is **not** the default design. It would become a hotspot file and create unnecessary write conflicts.

Preferred model:

```text
state/
  agents/
    mt.md
    hex.md
```

Each agent maintains its own state/log surface.

These files are not meant to be noisy command logs. They should only capture durable maintenance actions, such as:
- ingest
- query-backfill
- lint
- decision
- protocol
- handoff

Recommended entry template:

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

Rules:
- multiple agents may write their own `state/agents/<name>.md`
- do **not** introduce a shared root-level running log as the default
- root-level files should remain protocol-oriented, not high-conflict running logs
- if a global overview is needed later, prefer a read-oriented aggregation page instead of a shared write hotspot

This means:
- `VAULT.md` = protocol / maintenance contract
- `state/agents/*.md` = per-agent running state surface

## Immediate Next Step

Do not rush to physically redesign the whole vault.

The immediate next step is:

> **stabilize the schema layer first**

In practice, this means progressively formalizing:
- ingest protocol
- query-backfill protocol
- lint protocol
- multi-agent state/log surfaces

Only after these stabilize should physical directory redesign be reconsidered.
