# Masterplan Generator

**Spec-Driven Development for station software, in 20 prompts.**
From *The Spec Is the New Schematic*, John Carter N6YU, RSGB Convention 2026.

You answer five questions about your app and give Claude Code a screenshot of an app like the one you want. Then you paste 20 short prompts, one section at a time. Claude Code writes `masterplan.md`, an outline of your app that becomes its source of truth, and then builds the app from it.

**Start here → [masterplan-generator.md](masterplan-generator.md)**

![Example: the DX Spotter bandmap this generator was tested on](screenshot.png)

*Example target: an RBN + POTA CW bandmap. This screenshot was the only visual input for the test build.*

## How it works

1. **Before you start:** install Claude Code, open your app's folder, and hand the agent your screenshot. A short interview turns your answers into `idea.md`, and the agent gives the folder its own GitHub repo.
2. **Four sections, five prompts each,** pasted in order:

   | Section | Question it answers |
   |---|---|
   | Constitution | How we work: rules for building, testing and committing |
   | Spec | What the app does, with testable ranges |
   | Tech | What it's built with, and its modules |
   | Tasks | In what order: about 5 stages, each with a done-when line |

3. **After you finish:** tighten the masterplan, pressure-test it for ambiguity, commit, then start a fresh session and paste the build line.

Each prompt starts with a label, so the agent knows what to do and when:

| Label | The agent… |
|---|---|
| **RULE** | writes it into the masterplan and follows it during the build, not now |
| **DRAFT** | writes that part now and shows it to you for review |
| **USER INPUT** | asks you first, then writes down your answer |
| **SET** | writes your own answer or decision in as given |
| **PASTE** | treats it as a plain instruction |

## What you need

- [Claude Code](https://docs.claude.com) (check with `claude --version`)
- A GitHub account and the `gh` CLI, logged in
- A screenshot of an app like the one you want
- For apps with live data: the servers' addresses. The agent tests real servers before it builds any screens.

## Tested

The generator was run end to end as a novice test on 2026-09-30, building the DX Spotter shown above (NC7J AR-Cluster + POTA.app, Python/Tk, macOS). The app works. The test found 26 issues; the seven most important fixes are already in the generator.

- [generator-test-findings.md](generator-test-findings.md): every finding, with the observed behaviour and the proposed fix
- [generator-analysis.md](generator-analysis.md): findings grouped by generator section, plus the minimal set of changes
- [starter-critique-and-revision.md](starter-critique-and-revision.md): the earlier critique that introduced the labels

## Watch for

- **"Looks close" is not a pass.** Ask for the measured pass/fail output.
- **"Should work" means untested.** Ask for proof.
- **Two failed fixes on the same bug:** stop, and ask what was measured.
- **Never let the agent change a test just to make it pass.**
- **Your app needs its own repo,** even inside a `~/Projects` folder that is itself a repo.
- **Borrowed code:** note its license in the commit. GPL code makes your whole app GPL.

## Contact

John Carter, N6YU · john@n6yu.com · [github.com/jcarter-labs](https://github.com/jcarter-labs)
