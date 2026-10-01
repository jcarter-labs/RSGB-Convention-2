# masterplan-generator — novice test findings

Test run: 2026-09-30, folder `~/Projects/RSGB-3`, screenshot `spotter-linux.png`.
Status: in progress (step 5 redone). Fix after the test finishes.

## Test conditions
- Claude Code v2.1.286, Sonnet 5.5, Pro plan, effort medium
- Launched: `claude --strict-mcp-config --permission-mode default` (no MCP, manual approvals)
- claude-in-chrome built-in MCP disabled via `/mcp`
- `~/Projects/CLAUDE.md` renamed to `.off` for the run; `~/.claude/CLAUDE.md` (global, 1219 B) still active
- Stay in manual mode through step 6; switch to auto mode for sections 1–4 (note where)

## Findings (observed)
1. **Folder inside a parent repo.** `~/Projects` is itself a repo (dev-environment), which is a common setup.
   "If this folder has no GitHub repo" and Constitution #2 ("this folder's repo") both pass on the parent.
   - Fix: check that the repo root is *this* folder; if not, `git init` here.
2. **Parent repo leaks into the session.** Before `git init`, Claude Code picks up the parent repo's git status at startup.
   - Fix: have the agent create the repo early (step 3 or 5), not in "After you finish".
3. **Step 5 "any CLAUDE.md" is too broad.** The agent searched parent dirs and ran `git -C ~/Projects show HEAD:CLAUDE.md`,
   pulling a disabled CLAUDE.md out of git history.
   - Fix: "…and any CLAUDE.md in this folder…"
4. **Auto-mode nudge.** The first approval prompt suggests auto mode, and a novice will likely take it, which hides finding 3–type behavior.
   - Fix: add a line saying what to choose (e.g., Yes each time until the masterplan is saved; auto mode skips these checks).
5. **Step 1 has no install check.** Add `claude --version`.
6. **Legend omits PASTE**, and PASTE lines (steps 3–5) are sent before the legend exists.
   - Fix: define PASTE in the legend, or drop the PASTE: prefix from the pasted text.
7. **Model/usage banner.** Pro users see "Opus is now your default… draws down usage faster." A novice won't know what to pick.
   - Fix: one line of guidance on model choice.
8. **Existing ~/Projects/CLAUDE.md overlaps the generator.** For experienced users, shared rules and the masterplan Constitution can duplicate or conflict.
   The "keep your CLAUDE.md" advice is fine, but step 5's conflict check should be scoped (see 3).
9. **Step 5 overreach: the agent drafted the whole masterplan.** "Create masterplan.md with four sections" was read as "fill them in".
   It drafted Constitution/Spec/Tech/Tasks from idea.md + screenshot before any of the 20 prompts, which makes sections 1–4 redundant.
   A novice would likely approve the write and skip the generator's method entirely.
   - Fix: "Create masterplan.md with only the four section headings, empty; don't fill them in yet."
10. **Write-approval preview vanishes on reject.** The draft scrolled by in the approval prompt and disappeared when rejected; a novice won't know what happened.
    - Fix: note that Claude Code shows file writes for approval, and that rejecting leaves nothing saved.
11. **Conflict check worked well.** It found 5 idea.md-vs-screenshot conflicts (spot colors, default freq 14.045 vs 14.050, Linux screenshot vs Mac,
    fade style, POTA mode) and said which source it chose. Keep this; it should present conflicts for decision rather than pick silently.
12. **Agent offers to draft ahead.** After the Constitution RULEs, it noticed "measure against the Spec's screen list" has no screen list yet
    and offered to DRAFT the screen list and Tech now, before the Spec/Tech prompts. It asked first, which is good, but a novice would say yes
    and skip Spec #2 and all of Tech.
    - Fix: add to the legend: "Sections come in order; don't draft ahead. A RULE may refer to parts we haven't written yet."
13. **Example app is invisible to the agent.** Spec #1 said "modelled on N1MM" and the agent correctly refused to invent N1MM behaviour,
    then asked the user to supply any that matters. Constitution #1, Spec #1 and Tech #1 all assume the agent can see the example.
    A novice will likely reply "use what you know about N1MM", which gets unverified training-memory behaviour into the Spec.
    - Fix: idea.md Q1 asks for a link to the example (repo, manual, web page), plus "which of its behaviours matter to you".
14. **Screen-list positions are eyeballed.** Spec #2 gave positions read by eye (image 492x1189 px via sips), flagged them as approximate,
    and offered to write a pixel-measuring script now. The honesty is good, but writing a script during DRAFT is premature, and Constitution #3
    ("measure against the screen list") has nothing reliable to measure against until someone measures the reference.
    - Fix: Tasks should include an explicit early step: "measure the reference screenshot with a script; save reference measurements; layout checks use them."
    - Check: the agent cited "your note on pixel verification"; confirm its source (idea.md vs global ~/.claude/CLAUDE.md contamination).
15. **Claude Code auto-memory leaks prior-project feedback.** The pixel-verification idea came from the auto-memory index (MEMORY.md,
    entry "Verification rigor…"), loaded at session start. The agent used it without citing it until asked, then disclosed it properly.
    A true novice has no such memory; an experienced user's memory silently shapes the Spec.
    - Test fix: turn Auto-memory off in /memory for novice runs, or clear it.
    - Generator fix: step 5 conflict check should include "and any auto-memory notes you're using."
16. **Spec #2 surfaced good open gaps** (status dot/reconnect, fade curve, min window size, label spacing, POTA out-of-band drops) and listed
    them without deciding. That's good. Most belong to Spec #3 (testable ranges) or Spec #4 (data-source failures); the generator could say so.
17. **No label for "here is my decision".** RULE = follow during build, DRAFT = agent writes, USER INPUT = agent asks first.
    When the user answers open gaps with specific values, none fits; the agent correctly refused unlabeled text and asked which label to use.
    - Fix: add a label, e.g. "SET: write this into the named section as given," or allow "DRAFT: add these decisions as given."
18. **Agent's section names drift from the prompt numbering.** The user says "Spec #3/#4"; the masterplan has Summary/Features/Screen list.
    The agent also created a Features section with ranges before Spec #3 was sent (drafting ahead again, see 12).
    - Fix: step 5 creates the four sections with fixed subsections matching the 5 prompts in each (or the prompts name their subsection).
19. **Tasks came out as 21 fine-grained steps.** "Small ordered steps" produced 21 steps under 6 headings; real clients weren't tested
    against live servers until step 12 (only raw captures at 4–5). John's working pattern is ~5 stages: environment, data connections,
    main app, features, UI. Step count also drives how often the user must sit and approve.
    - Fix: Tasks #1: "Break the build into about 5 stages (environment, data connections, core logic, features, UI), each with sub-steps and a done-when line."
    - Fix: add a RULE: "Run each stage without stopping; stop for my review only at stage end, on a failed test, or for a decision."
20. **No git repo at Tasks time.** Confirms 1–2: the agent flagged that RSGB-3's repo root is still ~/Projects. The generator creates the repo only in
    "After you finish", so the whole planning phase has no version history.
    - Fix: git init + GitHub repo in Before you start (after step 3), commit idea.md and screenshot immediately.
21. **RULEs from all sections pile into Constitution.** The legend says "RULE: add it to masterplan.md as written" but not where.
    The agent filed Tech and Tasks RULEs (responsiveness, known limitations, simple-version-early, stage stops, pixel measuring) as
    Constitution items; it reached 11 rules, mixing "how we work" with tech and sequencing constraints.
    - Fix: legend says "RULE: add it to the section it's pasted under," or accept one Constitution list and cap it (~8).
    - Related: the agent caught that "simple version early" conflicted with the Tasks order (first window at stage 4) and asked before
      changing Tasks. Good; a walking-skeleton window was added at the end of stage 2.
22. **"Short outline" conflicts with testable detail.** After-you-finish #1 says "keep each section a short outline", but the plan reached
    270 lines because Spec #3/#4 and Tasks #2 demand testable ranges, match rules and done-when lines. The agent correctly refused to
    condense without asking (condensing would delete what the checks test against).
    - Fix: "Tighten masterplan.md: remove repetition and prose, keep every number, range, rule and done-when line."
    - Avoid splitting into a detail file: two sources drift, and Constitution #1 only names masterplan.md.
23. **Pressure-test was strong.** 18 items, ranked by risk; caught real spec gaps (spot age origin, repeat spots, fade boundary, span filter
    dropping spots too early, layout tolerance, reference size). Waited for approval; afterwards produced a per-item applied/line-number table on request.
    - Keep as is. Consider adding to the generator: "after applying, list each item with the line where it now appears."
24. **Rationale cites the user's setup.** Tech row for Python says "Matches the operator's other projects and global setup": knowledge from
    auto-memory / global CLAUDE.md, not from idea.md. Harmless here, but for a novice it would be invented context.
    - Test fix: disable auto-memory and rename ~/.claude/CLAUDE.md for a pure novice run.
25. **Claude Code's sandbox blocks the live cluster capture.** Telnet to nc7j.com:7373 is a non-HTTP port; the agent couldn't run the probe
    and asked the user to run it, allow unsandboxed network, or give another route. The generator's "test with real servers first" RULE
    collides with the sandbox, and a novice won't understand the three options.
    - Fix: Constitution #2's start-of-build check includes network reachability of every data source (host:port), with the remedy if blocked.
    - Fix: Before you start: "Some apps need network access the agent can't reach by itself; when asked, run the probe yourself in a second terminal."
    - Worked fine: the agent carried on with Stage 3 pieces that don't need the capture.
26. **Server-side state carries over by callsign.** The NC7J banner says skimmer spots are OFF by default, yet N6YU received skimmer spots
    at once: AR-Cluster stores each callsign's filters on the server, so earlier Spotter builds' settings were still active. A novice with a fresh
    callsign would get no skimmer spots and a "working" connection with an empty map.
    - Fix (app): send the needed filter commands explicitly on every login (e.g. "Set DX Filter Skimmer ..."), never rely on server defaults.
    - Fix (generator): Spec #4 "how we'll check each source" should include "don't rely on server-side settings from earlier sessions."
    - Confirmed from live capture: login prompt "login: " has no newline; post-login prompt "N6YU de NC7J <date> <time>Z arc6>";
      spot lines "DX de WA7LNW-#:  14020.0  XR4T  CW 13 dB 28 WPM CQ  0007Z" (skimmers carry -# and -1-#).

## Watch for (not yet observed)
- RULE lines acted on now instead of recorded (Constitution #2 most likely)
- DRAFT steps that start writing code
- USER INPUT steps that proceed without asking
- Step 4 interview: one question at a time? pushes back on vague answers?

## Authoring notes
- **Broken code fence** (15:18): an Obsidian edit dropped the step 4 PASTE line and its closing ```, which flipped rendering of every later block.
  Check: `grep -c '```' masterplan-generator.md` must be even. Commit known-good versions.
