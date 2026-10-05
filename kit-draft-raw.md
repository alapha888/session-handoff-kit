===DOC1===
---
name: session-handoff
description: Use this skill when context usage approaches capacity (>60%), before running /clear or /compact, when switching tasks, or when ending a coding session across Claude Code, Cursor, or Codex. Produces a structured handoff artifact that transfers execution state to a fresh agent session without loss of context or drift.
---

# Session Handoff Skill

## Purpose
Large context windows degrade in instruction adherence, reasoning precision, and tool accuracy as token counts accumulate. Long sessions fill with superseded plans, aborted scratchpads, and repetitive error traces. Resetting to a fresh session restores performance, but discarding context blindly introduces state loss, hallucinated progress, and repeated mistakes. 

The `session-handoff` skill standardizes how an agent snapshots active state. It translates ephemeral working memory into an actionable, verifiable bridge document for the next session.

---

## Workflow: Ending a Session (6 Steps)

Run this sequence when an agent session reaches operational limits or when halting work:

1. **Audit Git and Workspace State**
   Run `git status -s` and `git diff --stat` to establish grounded reality. Never rely on chat memory for what was modified. Identify modified, untracked, and staged files.

2. **Capture Goal State vs. Implemented Reality**
   Define the original objective in one sentence. Document precisely what has been verified as working (with passing test commands or runtime checks) versus what remains partial or unverified.

3. **Record Decisions Made and Discarded Paths**
   List architectural and design choices committed during the session, along with the concrete rationale. Explicitly record negative decisions (approaches tried and discarded) to prevent the next agent from re-attempting failed solutions.

4. **Catalog Files Touched and Relevant Symbols**
   List key file paths, functions, classes, and schema definitions modified or referenced. Include line references where relevant.

5. **Isolate Open Loops with Owners and Checks**
   List all unaddressed edge cases, failing tests, deferred tasks, and environment constraints. Every open loop must specify who resolves it (Human or Agent) and how completion is verified.

6. **Specify the Immediate Next Action**
   Draft a single, atomic, unambiguous instruction for the next agent to execute on turn one. Do not provide a broad list; specify the exact command to run or file to edit first.

---

## Operating Rules

- **Separate Facts from Speculation**: A test that passed is a fact; assuming code works because it compiled is speculation. Label untested assumptions explicitly as `[UNVERIFIED]`.
- **Mandate Owners and Verifications for Open Loops**: Every open loop must state an owner (`Human` or `Agent`) and a deterministic verification criteria (e.g., `pytest tests/auth/test_token.py:test_refresh_rotation passes`).
- **Ban Vague Continuations**: Never write "continue working on auth" or "finish frontend components." State the exact symbol, file, and failure mode.
- **Reference Real Workspace Identifiers**: All references to code must use exact file paths and symbol names. Do not use generalized pseudocode descriptions.

---

## Worked Example: Session Handoff Note

```markdown
# Session Handoff: JWT Rotation Implementation

### Goal & Current State
- **Target Goal**: Implement refresh token rotation and revocation in the FastAPI auth service.
- **Current State**: Refresh token database migration applied and rotation endpoint created. Token generation and database persistence work. Revocation blacklist cache is not yet wired into the dependency validator.
- **Verification**: `pytest tests/test_auth_tokens.py` passes (4/4 tests).

### Decisions Made (With Reasons)
- **Used Redis for Token Revocation List**: Selected Redis over Postgres lookups for revoked JTI tokens to keep latency under 5ms on protected routes.
- **Rejected Stateless Refresh Tokens**: We initially evaluated sliding expiration in JWT claims without persistence, but discarded it because compromised tokens could not be revoked before natural expiry.
- **Token Format**: Chose UUIDv4 for `jti` claim to maintain compatibility with existing session storage schemas.

### Files Changed
- `src/services/auth.py`: Added `rotate_refresh_token(old_token: str)` function and Redis invalidation call.
- `src/models/tokens.py`: Added `RefreshTokenRecord` SQLModel table for tracking token families.
- `alembic/versions/20261005_add_token_families.py`: Migration creating `token_records` table.
- `tests/test_auth_tokens.py`: Added unit tests for token family generation and rotation.

### Open Loops
- [Agent] Wire `verify_token_not_revoked` into `src/api/deps.py` -> Verify by checking `tests/test_auth_deps.py` fails on blacklisted token.
- [Agent] Handle database rollback if Redis is unreachable during rotation -> Verify by mocking Redis connection failure in unit test.
- [Human] Set `REDIS_AUTH_URL` secret in `.env.test` for CI execution.

### Next Action
Open `src/api/deps.py` and import `is_token_blacklisted` from `src.services.auth`. Insert the check into `get_current_user` directly after JWT decode verification.

### Watch-outs
- Do not modify the existing `access_token` expiration time (currently 15 minutes) in `src/core/config.py`.
- Alembic migration `20261005_add_token_families.py` must run before starting the API server locally (`alembic upgrade head`).
```

---

## Anti-Patterns

- **The Chat Transcript Dump**: Pasting raw conversation logs into the handoff note. The next session's context window is immediately polluted with the very conversational noise you intended to escape.
- **The Phantom Pass**: Marking a module "completed" when it has only been written, not executed or tested.
- **The Omitted Dead-End**: Leaving out solutions that failed. The next agent will read the codebase, see the same apparent path, and re-implement the failed approach.
- **The Abstract Next Step**: Ending with "Next: Implement remaining tests." The new agent spends 3-5 unnecessary turns discovering what needs testing.

===DOC2===
# Context Hygiene Checklist

Context degradation in AI coding sessions is silent. As context length increases, agents suffer from instruction fade, hallucinations, stale state references, and tool parameter errors. Use this operational checklist to maintain high-performance agent workflows.

---

## Operating Modes: When to Run What

| Command / Action | When to Use | Rough Threshold | Purpose |
| :--- | :--- | :--- | :--- |
| **`/compact`** | Continuing the exact same sub-task, but dialogue history contains noisy terminal outputs or long file reads. | **40% – 60% context** or **15–25 turns** | Flushes raw outputs and command histories while maintaining high-level thread continuity. |
| **`/clear`** | Switching to a completely different sub-task within the same codebase where earlier context is irrelevant. | Any time a distinct feature or bugfix boundary is crossed | Empties conversation memory completely while preserving the IDE state, active files, and workspace. |
| **Fresh Session** (New thread/window) | Completing an architectural phase, resolving major debugging loops, or when context exhibits degradation symptoms. | **>60% – 70% context** or **>35 turns** | Discards deep conversational drift, phantom file references, and degraded attention caches. |

---

## Context Degradation Indicators

Do not wait for a hard context ceiling error. Reset or compact immediately when you observe any of the following:

- **Instruction Amnesia**: Agent ignores negative constraints defined in earlier prompts (e.g., editing files it was told not to touch).
- **Repetitive Error Loops**: Agent suggests a fix it already tried 3 turns ago.
- **Diff Hallucinations**: Agent generates diffs against stale versions of files rather than current disk state.
- **Tool Failure**: Agent begins failing JSON syntax or schema validations on file-writing or terminal tools.
- **Sluggish Response**: High latency per generation turn caused by large KV-cache processing.

---

## What to Capture Before Resetting

Never clear or terminate a session without extracting these core data points:

1. **Active Diff and Branch**: Current git branch and list of dirty files (`git status -s`).
2. **Current Error Signature**: The exact compiler error, failed assertion, or runtime stack trace (top and bottom lines).
3. **Discarded Hypotheses**: What was attempted that failed, and why it failed.
4. **Immediate Target Assertion**: The specific passing criteria for the next prompt.

---

## The Pre-Reset 2-Minute Routine

Execute this checklist before wiping memory or launching a new session:

- [ ] **Step 1: Save Workspace State (30 seconds)**
  Ensure all files in the editor are saved. Run:
  ```bash
  git status -s
  ```
  If changes are functional, create a temporary commit (`git commit -m "wip: checkpoint before context reset"`). If incomplete, leave working files saved on disk.

- [ ] **Step 2: Run Verification Command (30 seconds)**
  Execute your test suite, type-checker, or linter:
  ```bash
  pytest <target_file> || npm test <target_file> || cargo check
  ```
  Capture the exact output status (pass/fail count or error message).

- [ ] **Step 3: Generate Handoff Note (45 seconds)**
  Fill out the standardized Handoff Note template (Doc 3) or command the active agent:
  > *"Generate a session handoff note using the session-handoff template. Document current state, decisions, files touched, open loops, and the exact next step."*

- [ ] **Step 4: Copy Note, Reset, and Bootstrap (15 seconds)**
  Copy the generated Markdown handoff note. Run `/clear` or open a fresh agent session. Paste the note as the first prompt:
  > *"We are resuming from a session reset. Here is our verified handoff state. Review the note and execute the Next Action specified."*

===DOC3===
# Session Handoff Note

<!-- 
One-line summary of overall objective and verification status of current code.
-->
### Goal & Current State
- **Goal**: <!-- What specific feature, bugfix, or refactor are we executing? -->
- **Current Status**: <!-- What is built and working right now? Be specific. -->
- **Verification Method**: <!-- What command proved this works? (e.g., pytest path/to/test, curl response code, npm run build) -->

<!-- 
Key technical decisions, patterns chosen, and alternative approaches explicitly rejected.
-->
### Decisions Made (With Reasons)
- **Decision 1**: <!-- Choice made --> — *Reason*: <!-- Why this approach was selected -->
- **Decision 2**: <!-- Choice made --> — *Reason*: <!-- Why this approach was selected -->
- **Discarded Approach**: <!-- What alternative was tried or considered and why was it abandoned? -->

<!-- 
Paths of files added, edited, or deleted, including key exported symbols or functions.
-->
### Files Changed
- `path/to/file1.ext`: <!-- What changed in this file? (e.g., added function X, updated schema Y) -->
- `path/to/file2.ext`: <!-- What changed in this file? -->
- `path/to/test_file.ext`: <!-- What tests were added or modified? -->

<!-- 
Unfinished work, unverified assumptions, deferred items, and required manual steps.
-->
### Open Loops
- [Agent] <!-- Unfinished task --> -> *Verification*: <!-- Specific check to confirm completion -->
- [Agent] <!-- Unhandled edge case or test failure --> -> *Verification*: <!-- Specific check to confirm completion -->
- [Human] <!-- External action required (e.g., setting credentials, installing system tool, approving migration) -->

<!-- 
Single, atomic, unambiguous instruction to execute immediately upon opening the fresh session.
-->
### Next Action
<!-- Specify the EXACT file to open, line to modify, or command to run. No vague directives. -->

<!-- 
Critical constraints, breaking changes, schema dependencies, or fragility to avoid.
-->
### Watch-outs
- <!-- What fragile part of the system must not be touched? -->
- <!-- Any environmental quirks, sequencing constraints, or flags to keep in mind? -->
