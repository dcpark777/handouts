A snapshot of this project is at ~/Downloads/fanout-new. It is this same project plus one
focused piece of work — the CLI/UI layer. Bring that work in without losing any of my local
changes. My current state is committed and tagged pre-ui-merge.

What the snapshot changed (use this to recognize its hunks):
- src/fanout/render.py rewritten as shared Rich layout helpers (header · body · summary · Next):
  status table with state glyphs, plan_panel, question_panel, review_header, summary_block,
  diff_block. write_progress (PROGRESS.md / progress.html) behaves the same.
- src/fanout/tui.py NEW: Textual review screen (ReviewApp, NoteModal) opened by `fanout review`
  on a TTY. Writes only review decisions; shipping stays in cli._ship.
- src/fanout/cli.py: imports the render helpers; `plan` prints plan_panel; `answer` prints
  question_panel; `review` gains --plain, rebases all reviewable targets up front, opens the
  TUI (falls back to prompts), ends with rich_status; `target` uses review_header and panels.
  No changes to run/answer logic, budgets, states, or _ship.
- src/fanout/plan.py: print_plan removed; Console import dropped.
- pyproject.toml: adds a `tui = ["textual>=0.80"]` extra.
- tests/test_fanout.py: two "Next:" assertions become "Next"; new test_review_tui_decisions.
- Docs: SPEC.md, README.md, CLAUDE.md (new UI rules section), skill/fanout/SKILL.md,
  skill/fanout/references/skill-authoring.md; NEW fixtures/skills/repo-inventory/SKILL.md.

Rules, for EVERY file in the snapshot:
1. src/fanout/workers/sdk.py: do not touch, ever.
2. File doesn't exist here: copy it in.
3. Byte-identical: skip.
4. Differs: diff mine vs snapshot. Check whether I changed it locally with
   `git diff $(git rev-list --max-parents=0 HEAD) HEAD -- <path>`.
   a. Only the snapshot changed it: copy it in.
   b. Only I changed it: keep mine.
   c. Both: if my change and the snapshot's are clearly separable per the description above,
      apply both and show me the resulting diff. If they touch the same lines, show me both
      sides and ask before choosing.
5. Anything in the snapshot that doesn't match the description above: don't apply; tell me.
6. pyproject.toml: keep my claude-agent-sdk pin no matter what.

Then: `uv sync --extra sdk --extra tui`, `uv run pytest -q` (expect 20 passed — if fewer, show
the failure and stop; do not edit sdk.py to fix it). Show me `git diff --stat` and a short
list of what was copied, merged, kept, or flagged. Stop before committing.