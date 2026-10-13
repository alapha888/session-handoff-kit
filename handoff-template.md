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
For each discarded approach, the reason is the load-bearing part: record why it was
tried and where it observably failed, or the next session will retry it with a
different parameter and fail at the same wall. Keep each field to one short line.
This template also works mid-session as a lightweight diagnostic: at a natural
breakpoint, try filling it from actual state — a field you cannot fill is a drift
alarm. Do not rewrite it on a schedule.
-->
### Decisions Made (With Reasons)
- **Decision 1**: <!-- Choice made --> — *Reason*: <!-- Why this approach was selected -->
- **Decision 2**: <!-- Choice made --> — *Reason*: <!-- Why this approach was selected -->

### Discarded Approaches (Dead Ends)
- **Approach**: <!-- What was tried or seriously considered -->
  - *Tried because*: <!-- What made it look promising -->
  - *Failed at*: <!-- The exact error, test result, or behavior that killed it -->
  - *Retry only if*: <!-- What would have to change before trying it again is rational -->

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
