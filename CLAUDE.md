# CLAUDE.md — Universal Best-Practice Template
# v2026.06b | Synthesised from Anthropic official docs, Karpathy/Forrest Chang,
# Marmelab, SmartScope, JD Hodges, and community research.
#
# HOW TO USE
# 1. Keep §§ 1–4 (Core Behavioral Rules) verbatim — they are universal.
# 2. Fill in §§ Project Context with YOUR project specifics.
# 3. Delete placeholder sections you don't need.
# 4. Target: under 150 lines total. Every line costs context tokens.
# 5. Use @path imports (bottom) to reference additional context files. Note:
#    imports load at session start and still consume tokens — they aid
#    organisation and reuse, not context reduction.
# 6. Use settings.json — NOT this file — for enforcement: permissions,
#    hooks, env vars. CLAUDE.md is advisory; settings.json is deterministic.
# 7. Commit to git so your team benefits. Use CLAUDE.local.md for personal
#    overrides (add to .gitignore).
# 8. Treat this file like code: prune it when things go wrong,
#    update it after every session retro.

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
- `/clear` between unrelated tasks. Long sessions with irrelevant context degrade quality.
- If you've been corrected twice on the same issue: stop, `/clear`, restart with a
  better prompt that incorporates what you learned. Persisting past two failures
  is slower than resetting.
- `/compact Focus on [X]` — be explicit about what to preserve; don't let
  auto-compaction decide unguided.
- Use subagents for exploration that reads many files. The research stays in a
  separate context window and won't pollute the main session.
- Don't run more parallel agents than you can review. Throughput gains are real;
  so is error compounding. At human pace, mistakes surface slowly. With many
  agents running unsupervised, small errors compound faster than you can catch them.
- Sessions start with ~20k tokens already consumed (system prompt + tools + this file)
  before you type anything. Treat context as a finite, precious resource.

## Verification Mandate

- Every substantive change must be verified by something that returns pass/fail.
  Acceptable signals: test suite, build exit code, linter, diff against fixture.
- Show evidence (test output, exact command + result) rather than asserting success.
- IMPORTANT: Hooks (not this file) are the right place for verification that must
  happen every single time. CLAUDE.md instructions are advisory; hooks are deterministic.

## Safety Rails

- IMPORTANT: Never write to .env, *.key, *.pem, or any secrets file.
- IMPORTANT: Never suppress errors to make a build pass — fix root causes.
- IMPORTANT: Never touch files outside the task scope without being asked.
- File-level protection belongs in settings.json → permissions.deny, not here.
- Run /security-review periodically. It won't catch everything — that's still your job.

## Decision Log

- Record non-obvious decisions and their side-effects in a git-tracked log (e.g.
  `docs/decisions.md` or ADR files). A recorded decision that omits its consequences
  is still a gap.
- This applies especially to: architectural choices, deliberate trade-offs,
  features removed or deferred, and anything where "why didn't we do X?" will be
  a reasonable question in three months.
- If a decision silently affects other parts of the system, record those side-effects
  explicitly — not just the decision itself.

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
