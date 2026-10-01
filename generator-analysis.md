# masterplan-generator — Lessons from the Novice Test Run

**Run:** 2026-09-30 · folder `~/Projects/RSGB-3` · screenshot `spotter-linux.png` · Claude Code v2.1.286, Sonnet 5.5, Pro plan
**Outcome:** the app works. The generator's method held up; the failures were at the edges: setup, labels, scope control and live data.
**Source:** 26 logged findings in `generator-test-findings.md` (numbers in brackets below, e.g. [9]).

---

## 1. What worked (keep as is)

- **idea.md interview + screenshot.** These gave the agent enough to find 5 real conflicts on its first read [11].
- **Labels.** The agent refused unlabeled text and asked which label applied [17]. The labels are doing their job.
- **The pressure test.** It found 18 ranked items, several of them real spec bugs (spot age origin, repeat spots, fade boundary, span filter dropping spots early). It waited for approval before applying anything [23].
- **"Open until a live session."** The agent wouldn't write server details from memory. Every live fact came from a capture or a session.
- **Testable ranges and done-when lines.** These made the build checkable and kept "looks close" out of the pass criteria.

## 2. Before you start

| Observed | Change |
|---|---|
| No install check [5] | Step 1: add `claude --version`. |
| `~/Projects` is itself a repo, which is common. "If this folder has no GitHub repo" passes on the parent [1, 20] | New step after the screenshot: *"Make this folder its own git repo root (`git init` here even if a parent folder is a repo). Create a GitHub repo, private or public as I choose, and commit idea.md and my screenshot."* |
| Before `git init`, the parent repo's status leaks into the session [2] | Same fix: the repo is created at the start, not in "After you finish". |
| The approval prompt pushes the user toward auto mode; a rejected write's preview vanishes [4, 10] | Add one note: *"The agent asks before running commands or writing files. During planning, read each request and choose Yes or No. Auto mode is for the build. If you reject a write, nothing is saved."* |
| The model banner confuses a novice on the Pro plan [7] | One line: which model to use for planning versus the build, and that Opus uses up limits faster. |
| Live servers on non-HTTP ports are blocked by the sandbox [25] | Note: *"If the agent can't reach a server, it will ask. Run the command it gives you in a second terminal, or allow it for that host only."* |

## 3. Legend and labels

| Observed | Change |
|---|---|
| PASTE isn't defined, and PASTE lines are sent before the legend [6] | Define PASTE in the legend, or drop the prefix from the text being pasted. |
| The agent drafts ahead: it offered to write the screen list and Tech while still on the Constitution, and built a "Features" section before Spec #3 [12, 18] | Add to the legend: *"Sections come in order; don't draft ahead. A RULE may refer to parts not written yet."* |
| There's no label for "here is my answer" [17] | Add **SET:** *"write this into the section as given."* |
| RULEs from every section pile into Constitution, which reached 11 items [21] | Legend: *"RULE: add it to the section it's pasted under."* |

## 4. Setup line (step 5) — the biggest fix

| Observed | Change |
|---|---|
| "Create masterplan.md with four sections" was read as "fill them in", and the agent drafted the whole plan [9] | *"Create masterplan.md with only the four section headings, empty; don't fill them in."* |
| "any CLAUDE.md" sent the agent into parent folders and git history [3] | *"…any CLAUDE.md **in this folder**…"* |
| The agent picked answers for conflicts without asking; it also used auto-memory notes without citing them [11, 15] | *"List each conflict, and any memory notes you're using, one line each, for me to decide."* |

## 5. Constitution

| Observed | Change |
|---|---|
| "This folder's repo" passes on a parent repo [1] | Constitution #2: *"…check this folder is its own repo root, and that each data source's host:port is reachable…"* |
| The user kept having to approve each step [19] | Add RULE: *"Run each stage without stopping; stop only at stage end, on a failed test, after two failed fixes, or for my decision."* |
| Pixel verification was only implied; it came from the user's own memory, not the generator [14, 15] | Add RULE: *"Measure UI layout with pixels against reference measurements, within a stated tolerance; never claim 'matches' from a visual impression."* |

## 6. Spec

| Observed | Change |
|---|---|
| The agent can't see the example app, so it invents or omits its behaviour [13] | idea.md Q1: *"…link to the example if you have one (repo, manual, page), and which of its behaviours matter to you."* |
| The server stored N6YU's old filters, so a novice with a fresh callsign would get an empty map [26] | Spec #4: *"…and how we'll check each source works, including after reconnect. Send every server setting explicitly on each connect; never rely on settings from earlier sessions."* |
| Section names drift from the prompt numbering [18] | Step 5 creates fixed subsections, or each prompt names its subsection (Summary, Screen list, Features, Data sources, Scope). |

## 7. Tech

- No changes needed. The USER INPUT choices (socket in a worker thread, Pillow + pytest) were explained well and easy to decide.
- One observation: rationale lines can cite the user's own setup ("matches the operator's other projects") [24]. The step 5 memory-disclosure fix covers this.

## 8. Tasks

| Observed | Change |
|---|---|
| 21 fine-grained steps; real clients weren't tested against live servers until step 12 [19] | Tasks #1: *"Break the build into about 5 stages (environment, data connections, core logic, features, UI), each with sub-steps and one done-when line."* |
| "Simple version early" conflicted with the order, since the first window came at stage 4 | Tasks #4: *"…a first bare window showing live data right after data connections work."* (This walking-skeleton step was added during the run and worked.) |
| Screen-list positions were eyeballed, with nothing reliable to measure against [14] | Tasks #2: *"…the first step measures the reference screenshot with a script; layout checks use those numbers."* |

## 9. After you finish

| Observed | Change |
|---|---|
| "Keep each section a short outline" conflicts with testable detail; the plan reached 270 lines [22] | #1: *"Tighten masterplan.md: remove repetition and prose; keep every number, range, rule and done-when line."* |
| The pressure test produced a fix list but no proof the fixes were applied [23] | #3: add *"…after applying, list each item with the line where it now appears."* |
| The GitHub step comes too late [20] | #4 moves to Before you start (see §2). |

## 10. Running a clean novice test (test method, not generator text)

Your own setup got into the test in **six** places. Before the next run:

- `~/Projects/CLAUDE.md` loads from the parent folder, and it can still be read from **git history** after renaming [3, 8].
- The global `~/.claude/CLAUDE.md` [24].
- Claude Code **auto-memory** (MEMORY.md) [15].
- MCP servers, including the GitHub MCP and claude-in-chrome. Use `--strict-mcp-config` and turn them off in `/mcp`.
- A saved **auto mode** default. Use `--permission-mode default`.
- **Server-side state** stored by callsign (NC7J filters) [26].

**Authoring note:** an Obsidian edit dropped a ``` fence, and the rendering of everything after it flipped. Check that `grep -c '```' masterplan-generator.md` is even, and commit known-good versions.

---

## Summary: recommended minimal set of changes

These seven changes fix most of what went wrong. Each one is a text edit to the generator.

1. **Setup line (step 5):** *"Create masterplan.md with only the four section headings, empty; don't fill them in. Read idea.md, my screenshot and any CLAUDE.md in this folder; list conflicts, and any memory notes you're using, one line each, for me to decide."* [3, 9, 11, 15]
2. **Legend:** add *"Sections come in order; don't draft ahead,"* *"RULE goes in the section it's pasted under,"* and a **SET:** label for decisions. [12, 17, 21]
3. **Repo at the start:** after the screenshot step, `git init` in this folder even if a parent is a repo, create the GitHub repo, and commit. Constitution #2 then checks that this folder is its own repo root. [1, 2, 20]
4. **Tasks as about 5 stages:** environment → data connections (live) → bare window with live data → core logic → features → UI. The first step measures the reference screenshot. Add a RULE: *"stop only at stage end, on a failed test, after two failed fixes, or for my decision."* [14, 19]
5. **Live data:** Spec #4 says *"send every server setting explicitly on each connect,"* and Constitution #2 checks that each data source's host:port is reachable. Before you start explains running a probe in a second terminal when the sandbox blocks it. [25, 26]
6. **After you finish #1:** *"Tighten: remove repetition; keep every number, range, rule and done-when line."* [22]
7. **idea.md Q1:** ask for a link to the example app and which of its behaviours matter. [13]
