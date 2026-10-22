# Session Handoff Kit

A free kit for users of AI coding agents (Claude Code, Codex, Cursor, Gemini CLI): end every session with a structured handoff, so the next session — or the next agent — starts from facts, not from a degraded, over-full context.

## What's inside

| File | What it is |
| --- | --- |
| `SKILL.md` | A ready-to-install agent skill: when context fills up or a session ends, the agent produces a structured handoff note (goal state, decisions and why, files touched, open loops, next action). |
| `context-hygiene-checklist.md` | One-page decision guide: when to `/clear`, when to `/compact`, when to start fresh — and the 2-minute routine to run before any reset. |
| `handoff-template.md` | A fill-in handoff note template with per-section guidance. |

Read the checklist free as a Gist: [Context Hygiene Checklist for AI Coding Agents](https://gist.github.com/alapha888/7c7f0241ccd23d8a479a47ccaacf4b83).

## Install the skill

Copy `SKILL.md` into a `session-handoff` folder in your agent's skills directory, or use the skills CLI:

```bash
npx skills add alapha888/session-handoff-kit
```

Gemini CLI natively supports the Agent Skills standard: install with `npx skills add alapha888/session-handoff-kit --agent gemini-cli`, or copy `SKILL.md` into `~/.gemini/skills/session-handoff/` (user level) or `.gemini/skills/session-handoff/` (project level).

## When you may not need this

If your agent's harness already handles compaction, sub-agent spawning, and memory management well, an extra handoff ritual can conflict with it instead of helping. That point was made by a maintainer reviewing this skill for GitHub's awesome-copilot collection ([PR #5097](https://github.com/github/awesome-copilot/pull/5097), closed Oct 22, 2026), and it is fair.

This kit earns its keep in the gaps a single harness does not own:

- Switching between different agents or tools (Claude Code → Codex → Gemini CLI), where no shared memory exists.
- Resuming after days, not minutes, when you want a human-readable record of decisions and why they were made.
- Working in a harness with no built-in compaction or memory handling.

If none of those apply, skip the skill and let the harness do its job.

Full write-up: [My session-handoff skill was rejected from awesome-copilot — the maintainer had a point](https://dev.to/alapha888/my-session-handoff-skill-was-rejected-from-awesome-copilot-the-maintainer-had-a-point-4kod) (dev.to, Oct 22, 2026).

## Why

Agent output quality drops as context fills with stale exploration. The fix is not a bigger context window; it is a habit: capture state before you reset, in a format the next session can execute from without re-asking you everything.

MIT licensed. Feedback via GitHub issues.

## Install via skills.sh

Listed at [skills.sh/alapha888/session-handoff-kit](https://skills.sh/alapha888/session-handoff-kit).

Listed on OpenAgentSkill: [alapha888-session-handoff-kit](https://www.openagentskill.com/skills/alapha888-session-handoff-kit)

Included in [sickn33/agentic-awesome-skills](https://github.com/sickn33/agentic-awesome-skills), a curated registry of agent skills — find it under `skills/session-handoff` in that repository.

Also listed on [AwesomeSkills.dev](https://www.awesomeskills.dev/en/skill/alapha888-session-handoff-kit) and Skillstore.

Also indexed on [SkillsMP](https://skillsmp.com/creators/alapha888/session-handoff-kit/skill).

Listed in [Piebald-AI/awesome-gemini-cli](https://github.com/Piebald-AI/awesome-gemini-cli), a curated list of Gemini CLI resources, under Development Tools & Utilities (PR #215 merged Oct 15, 2026).

Listed in [Chat2AnyLLM/awesome-claude-skills](https://github.com/Chat2AnyLLM/awesome-claude-skills), a catalog of Claude Code skills and resources — listed with 1 skill (count corrected Oct 17, 2026).

```bash
npx skills add alapha888/session-handoff-kit
```

Adds the `session-handoff` skill to your agent environment. Free to use. No account required.

## More tools by the same author

- **Agent Skills Pro** — Three advanced skills for AI coding agents: multi-agent task decomposition, codebase mapping, and release notes from git history. $39 one-time. https://alapha888.github.io/agent-skills-pro/
- **Agent Skills (English)** — Five free skills for commit messages, code review, deep research, meeting notes, and technical-writing proofreading. Free, MIT. https://github.com/alapha888/agent-skills-en
- **RunnerDriftAudit** — Free CLI that scans GitHub Actions workflows for runner-label and tool drift ahead of the ubuntu-latest 26.04 migration; the Pro edition ($29 one-time) scans a whole local workspace in one run. https://github.com/alapha888/runner-drift-audit
- **PubAudit** — Free CLI that batch-converts Microsoft Publisher (.pub) files to PDF and verifies every output (OK / REVIEW / FAILED). https://github.com/alapha888/pubaudit
- Full index of free tools, paid tiers, and waitlists: https://gist.github.com/alapha888/fe51434e80dbc0ef5610aeab38b025f4

## Validating next (not yet built)

- **MDClip Pro** — web page → Markdown converter; planned ¥15/month *(not built yet, no payment taken)*. Waitlist: https://alapha888.github.io/mdclip-waitlist/ — builds only if 30 people sign up by Oct 31, 2026.
- **SyncMatrix** — multi-account publishing management for matrix/team operators; planned ¥39/month *(not built yet, no payment taken)*. Waitlist: https://alapha888.github.io/syncmatrix-waitlist/ — builds only if 20 people sign up by Oct 31, 2026.
