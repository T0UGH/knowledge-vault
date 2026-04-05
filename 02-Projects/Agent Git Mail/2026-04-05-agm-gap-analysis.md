# AGM gap analysis (2026-04-05)

## Goal

Assess whether AGM is already past toy stage, based on current code rather than README, and identify the highest-value gaps to close next.

## Verification evidence

- Reviewed core implementation under `packages/agm/src`
- Reviewed representative tests under `packages/agm/test`
- Ran test suite in `packages/agm`
  - result: **13 test files, 58 tests passed**
  - note: `npm test -- --runInBand` failed because Vitest does not support that flag in this setup; plain `npm test` passed

## Current system state

AGM is already more than a README concept. The codebase has a real, test-covered MVP loop:

- profile-based config model
- send / reply / read / list / archive commands
- mailbox dual-write model
- contact cache cloning / refresh
- daemon polling + waterline tracking
- activation / host ingress integration
- runtime event logging
- doctor checks

This means AGM is **not a toy concept**, but it is also **not yet a hardened 1.0 transport**.

## What is already solid

### 1. MVP mailbox loop exists in code

Core flows are implemented:

- `src/app/send-message.ts`
- `src/app/reply-message.ts`
- `src/app/read-message.ts`
- `src/app/list-messages.ts`
- `src/app/archive-message.ts`
- `src/app/run-daemon.ts`

The transport model is clear: sender outbox + recipient inbox, with Git commits on both sides.

### 2. Tests cover happy-path core behavior

Representative passing tests:

- `test/send.test.ts`
- `test/reply.test.ts`
- `test/archive.test.ts`
- `test/daemon.test.ts`
- `test/config.test.ts`
- `test/git-waterline.test.ts`
- `test/push-behavior.test.ts`

This is enough to say the project has crossed the pure-concept threshold.

### 3. Runtime observability has started

There is already a usable runtime observability slice:

- structured event types
- daemon poll start/finish logs
- activation sent / failed / skipped logs
- doctor checks for config / git / runtime / state

That is a real system move, not toy-project theater.

## Highest-value gaps

### Gap 1: protocol semantics are still too thin

Current message model is lightweight, but several critical semantics are still underdefined:

- what exactly counts as the canonical message ID
- what duplicate delivery means
- whether delivery semantics are best-effort / at-least-once / something else
- whether reply chains are validated or only syntactic
- where seen / processed / obligation semantics begin and end

Current code has practical behavior, but the protocol truth is still partly implicit in implementation.

### Gap 2: failure model is not first-class yet

This is the biggest system gap.

The code handles happy path and some local failure cases, but there is not yet a clearly defined failure model for:

- concurrent writers to the same mailbox
- push rejection / non-fast-forward recovery
- partial dual-write success (sender committed, recipient failed)
- activation failure retry policy
- repeated daemon detection after partial handling
- idempotent re-processing after crash/restart

Without this, the project is still an MVP transport, not a hardened one.

### Gap 3: daemon / wakeup semantics are only partially productized

The daemon has real mechanics:

- polling
- waterline
- activation checkpointing
- host adapter fallback

But the system contract is still not fully explicit:

- what exactly the waterline means on first run
- whether first-run backfill should notify or not in all modes
- what guarantees checkpointing provides
- how activation retry should interact with checkpoint state
- how to confirm wakeup success vs only wakeup attempt

Current implementation is good enough for MVP, but the wakeup contract is not yet fully closed.

### Gap 4: Git conflict strategy is under-specified

For a Git-backed transport, this is a core risk area.

Today there is not enough explicit handling for:

- concurrent send/reply to same target repo
- rebase conflicts on pull
- non-fast-forward push failures
- remote drift between cache clone and remote truth
- duplicate commits representing same logical mail

This is the area most likely to make the system feel “toy” under real load.

### Gap 5: doctor/log are useful, but still shallow

Current doctor checks are a good start, but still limited:

- no end-to-end delivery health check
- no mailbox consistency check
- no detection of partial dual-write divergence
- no stale contact-cache diagnostic
- no “message delivered but not activated” diagnosis
- no “activation sent but host did not ingest” diagnosis

This is polish debt, not MVP debt, but it matters for seriousness.

### Gap 6: tests are strong on happy path, lighter on adverse cases

Current coverage is good enough to trust the MVP. It is not yet good enough to trust edge behavior.

Missing or thin areas include:

- non-fast-forward push failure tests
- concurrent write tests
- contact cache origin drift tests beyond basic mismatch
- activation retry / dedupe tests
- host adapter failure and recovery tests
- malformed / duplicate message scenarios
- partial write rollback or compensating behavior tests

### Gap 7: some signs of unfinishedness remain in repo hygiene

Examples:

- debug logs still exist in `test/reply.test.ts`
- built `dist/` is checked in alongside source, which is fine if intentional, but raises release-discipline questions if not
- `.env` exists untracked in repo root

These are not architectural blockers, but they affect project sharpness.

## Recommended decomposition

### Phase A: semantics closure

Close the written contract before adding new features.

1. define message ID truth
2. define delivery semantics
3. define reply semantics
4. define activation/checkpoint semantics
5. define first-run / replay semantics

Output:

- protocol doc
- README alignment
- tests asserting those semantics

### Phase B: failure-model hardening

Focus on transport reliability under non-happy paths.

1. document failure matrix
2. handle push/pull conflict classes explicitly
3. decide retry vs fail-fast per step
4. define partial dual-write recovery strategy
5. test crash/retry/idempotency behavior

Output:

- clearer error classes
- more deterministic retry behavior
- adverse-case tests

### Phase C: observability and diagnosis

Make operations less guessy.

1. enrich event taxonomy
2. add end-to-end doctor checks
3. add cache divergence checks
4. add activation-ingress visibility checks
5. improve log filtering and summaries

Output:

- easier diagnosis
- higher confidence in real use

### Phase D: product sharpness / 1.0 polish

1. remove stray debug prints
2. tighten help / command UX
3. validate README against actual CLI and runtime behavior
4. decide release packaging discipline around `dist/`
5. add a minimal “production readiness” checklist

## My overall judgment

AGM is **not a toy system** anymore.

A more accurate label is:

> a real MVP transport with working code and tests, but still missing protocol hardening and failure semantics needed for a serious 1.0

## Immediate next priorities

If only 3 things get done next, they should be:

1. **write down protocol semantics clearly**
2. **close the failure model for dual-write + wakeup**
3. **add tests for non-happy-path transport behavior**
