 # CLAUDE.md — Universal Best-Practice Template
# Edition 2026-06-26 | Synthesised from Anthropic's official docs and a watchlist of
# practitioner sources, with every technical claim verified against the docs.
#
# HOW TO USE
# 1. Keep §§ 1–4 (Core Behavioral Rules) verbatim — they are universal.
# 2. Fill in §§ Project Context with YOUR project specifics.
# 3. Delete placeholder sections you don't need.
# 4. Target under 200 lines. Every line costs tokens, and a bloated file gets
#    ignored — for each line ask "would removing this cause a mistake?" If not, cut.
# 5. Use @path imports (bottom) to reference additional context files. Note:
#    imports load at session start and still consume tokens — they aid
#    organisation and reuse, not context reduction.
# 6. Use settings.json — NOT this file — for enforcement: permissions,
#    hooks, env vars. CLAUDE.md is advisory; settings.json is deterministic.
# 7. Commit to git so your team benefits. Use CLAUDE.local.md for personal
#    overrides (add to .gitignore).
# 8. Treat this file like code: prune when things go wrong; add a line the moment you'd
#    otherwise repeat yourself — same mistake twice, a review catches what it should've
#    known, or a new teammate would need the context. If Claude keeps breaking a rule
#    that IS written here, the file is too long and the rule is lost — prune it, or
#    convert that rule to a hook.
# 9. <!-- HTML comments --> are stripped before this file loads — free for
#    maintainer notes; the '#' lines and prose above are not, so keep them lean.

---

## 1. Think Before Coding
**Don't assume. Surface ambiguity. Ask.**

- State assumptions explicitly before implementing. If uncertain, ask.
- If multiple interpretations exist, name them — don't silently pick one.
- If a simpler approach exists, say so and offer the tradeoff.
- If something is unclear, stop. Name what's confusing. Ask.
- For larger features: ask to interview me with the AskUserQuestion tool before
  writing a single line of code. Build a SPEC.md, then start a fresh session.

## 2. Simplicity First
**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for scenarios that can't actually occur.
- If 50 lines solves it, don't write 200.
- Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.
- Run /simplify before declaring a task complete.

## 3. Surgical Changes
**Touch only what you must. Clean up only your own mess.**

- Don't "improve" adjacent code, comments, or formatting unless asked.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.
- Remove imports/variables/functions that YOUR changes made unused.
- The test: every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution
**Define success criteria. Loop until verified.**

- Transform every task into a verifiable goal before starting:
  - "Fix the bug" → reproduce it, then fix it, then confirm it's gone
  - "Add validation" → define what invalid input looks like, implement, verify
  - "Refactor X" → confirm behaviour is identical before and after
- For multi-step tasks, state a plan first: `Step → verify: [check]`
- Always run the relevant check after changes. Show the output — don't assert success.
- IMPORTANT: Never claim a task is done without running the check.
- The "check" is whatever returns a pass/fail signal in this project: a test suite
  if one exists, a build exit code, a linter, or a defined manual verification step.
  The rule is "have a check and run it" — not "have unit tests."

---

## Session & Context Management

- Use plan mode for anything that touches multiple files or has uncertain scope.
  Explore → plan → implement → commit. (Activate via Shift+Tab in the CLI.)
  But if you could describe the diff in one sentence, skip the plan — it's overhead.
- `/clear` between unrelated tasks. Long sessions with irrelevant context degrade quality.
- If you've been corrected twice on the same issue: stop, `/clear`, restart with a
  better prompt that incorporates what you learned. Persisting past two failures
  is slower than resetting.
- Recover instead of fighting a bad path: `/rewind` (or double-`Esc` on an empty prompt)
  rolls back code and/or conversation to a checkpoint — but checkpoints cover only Claude's
  own edits, so they're no substitute for git. Use `/btw` for throwaway questions you don't
  want entering the session's history.
- `/compact Focus on [X]` — be explicit about what to preserve; don't let
  auto-compaction decide unguided. You can also bake this into CLAUDE.md (e.g. "When
  compacting, always preserve the list of modified files and any test commands") so it
  survives every summarisation. The project-root CLAUDE.md re-injects after /compact;
  nested CLAUDE.md files and chat-only instructions don't, so keep must-survive rules in
  the root file.
- Use subagents for exploration that reads many files — the research stays in a
  separate context window and won't pollute the main session. Scope each one narrowly.
  Most subagents (custom and built-in) inherit your full CLAUDE.md/memory hierarchy, but
  the built-in Explore and Plan agents skip it (and git status) to stay fast — so restate
  any rule they must follow in the delegation prompt.
- Don't run more parallel agents than you can review. Throughput gains are real;
  so is error compounding. At human pace, mistakes surface slowly. With many
  agents running unsupervised, small errors compound faster than you can catch them.
- Sessions start with ~20k tokens already consumed (system prompt + tools + this file)
  before you type anything. Treat context as a finite, precious resource.
- Growing too long? Split rules into `.claude/rules/*.md`. A rule with a `paths:` glob
  loads only when Claude reads a matching file — modular, and you don't pay context for
  it until it's relevant.
- Auto memory (on by default, v2.1.59+): a second system Claude maintains in
  `~/.claude/projects/<project>/memory/` — the first 200 lines (or 25KB) of `MEMORY.md`
  load each session. It complements the CLAUDE.md you curate; review it and toggle via
  `/memory`. Asking Claude to "remember" X saves to auto memory by default — say "add
  this to CLAUDE.md" when you want it curated here instead.

## Verification Mandate

- Every substantive change must be verified by something that returns pass/fail.
  Acceptable signals: test suite, build exit code, linter, diff against fixture.
- Show evidence (test output, exact command + result) rather than asserting success.
- IMPORTANT: Hooks (not this file) are the right place for verification that must
  happen every single time. CLAUDE.md instructions are advisory; hooks are deterministic.
- For a check that must pass before a turn ends, a Stop hook blocks the turn until your
  script succeeds (Claude Code ends it after 8 consecutive blocks); or set a `/goal`
  condition and a separate evaluator keeps Claude working until it holds. Both let an
  unattended run finish honestly — deterministic where this file is only advisory.
- Before declaring done, have a fresh subagent review the diff against the
  requirements — it sees only the change, not the reasoning behind it. Tell it to flag
  only gaps affecting correctness or stated requirements, or it will invent work.

## Safety Rails

- IMPORTANT: Never write to .env, *.key, *.pem, or any secrets file.
- IMPORTANT: Never suppress errors to make a build pass — fix root causes.
- IMPORTANT: Never touch files outside the task scope without being asked.
- File-level protection belongs in settings.json → permissions.deny, not here.
- Permissions are enforced by Claude Code, not the model: a deny rule or a PreToolUse
  hook blocks an action no matter what this file or your prompt says. Use them for
  hard boundaries; use this file for guidance.
- Run /security-review periodically. It won't catch everything — that's still your job.

## Decision Log

- Record non-obvious decisions in a git-tracked log (e.g. `docs/decisions.md` or ADR
  files) — architectural choices, deliberate trade-offs, anything deferred or removed,
  and any "why didn't we do X?" a future reader will ask. Always capture the
  side-effects too: a decision logged without its consequences is still a gap.

---

## What Goes Where (quick reference)

| What                                | Where                      |
|-------------------------------------|----------------------------|
| Universal behavioral rules          | This file (§§ 1–4 above)   |
| Project-specific code conventions   | This file or .claude/rules/|
| Build/test/lint commands            | This file                  |
| Repetitive multi-step workflows     | .claude/skills/            |
| Specialized sub-agent personas      | .claude/agents/            |
| Permission enforcement (deny/allow) | settings.json → permissions|
| Auto-run linter/formatter on save   | settings.json → hooks      |
| External tool connections           | settings.json → MCP        |
| Personal preferences (not shared)   | ~/.claude/CLAUDE.md        |

---

## Project Context  ← CUSTOMISE THIS SECTION

### Stack
<!-- e.g. FastAPI + React + PostgreSQL | Node 22 | Python 3.12 -->

### Commands
```bash
# Dev server:
# Tests (run after every change):
# Lint:
# Type-check:
# Build:
```

### Code Conventions
<!-- Indentation, naming, import style, error-handling approach -->
<!-- Only include rules that differ from language defaults -->

### Architecture
<!-- Key directories and their purpose -->
<!-- Any non-obvious data-flow or request lifecycle -->
<!-- Decisions Claude can't infer from reading the code -->

### Gotchas & Environment Quirks
<!-- Required env vars, known footguns, migration patterns, soft-delete rules, etc. -->
<!-- After every session retro, add learnings here -->

---

## Additional Context (imports)
<!-- @README.md -->
<!-- @docs/architecture.md -->
<!-- @~/.claude/CLAUDE.md  (personal global preferences) -->

Claude Code reads CLAUDE.md, **not** AGENTS.md. If your repo already uses AGENTS.md, don't
maintain two files — import it here (`@AGENTS.md`) or symlink (`ln -s AGENTS.md CLAUDE.md`).
