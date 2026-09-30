# Masterplan Starter: Critique and Proposed Revision

*N6YU, RSGB Convention 2026. Draft for review, revision 2, 2026-09-30.*
*Compares `masterplan-starter.md` (RSGB-Convention-2026 @ 9d37160) with `masterplan-v2.md` (spotter-win3 @ 4a920ad).*

This is a proposal to improve the starter. It does not try to make the starter more like v2.

---

## 1. The core problem

Every starter line is in the user's voice, addressed to Claude. That part is fine. The confusion is that the lines ask Claude for **three different kinds of action, at different times**, and nothing tells Claude which is which.

**Label key** *(explains the three labels to the reader; you don't paste this table)*

| Label | What Claude does | When |
|---|---|---|
| **RULE** | Writes it into masterplan.md as written, and obeys it later | During the build, not now |
| **DRAFT** | Writes that part of masterplan.md, then shows it for your review | Now |
| **USER INPUT** | Asks you first, then writes down your answer | Now; you decide |

The **legend** is the paste block that starts "Each line I send…" (section 4). It tells *Claude* the same thing this table tells *you*.

### How today's 20 lines fall into these kinds

| Section | RULE | DRAFT | USER INPUT | Mixed / unclear |
|---|---|---|---|---|
| Constitution | 1, 3, 4, 5 | | | 2 (reads as "do it now") |
| Spec | | 1, 2, 3 | 5 (Claude will invent it) | 4 (a draft *plus* a build-time rule) |
| Tech | 3, 4, 5 | | 1 (the choice), 2 (the platforms) | |
| Tasks | 3, 4 | 1, 2 | | 5 (build-time rule; repeats Constitution 4) |

### What goes wrong for a beginner

- **Rules pasted one at a time get carried out.** "Before any code, check my tools…" usually makes Claude run the checks immediately, before any masterplan exists.
- **Rules can't be checked yet.** "Check my tools" has no context: which platform? which language? Neither has been decided yet.
- **Decisions get made without the user.** Spec 5 (must / won't) and Tech 1 (the stack) read as tasks, so Claude fills them in and the user is never asked.
- **Forward references.** Constitution 3 points to "the Spec's screen list" before the Spec exists.
- **The intro misleads.** It says "Claude writes the answers into masterplan.md", which suggests a question-and-answer session, but most lines aren't questions.
- **Nothing separates what to paste from what to read.** "Fill in the brackets" sits next to paste-able prompts, and the brackets themselves are easy to paste unfilled.

---

## 2. Comparison with v2, section by section

v2 is an **output**: finished, settled rules and facts. The starter is a **generator**, so it has to say *rule*, *draft* or *user input*, which v2 never needs to. The table below uses v2 only to find gaps.

| Section | v2 (Spotter's finished masterplan) | Starter (produces a masterplan) | Gap worth closing in the starter |
|---|---|---|---|
| Constitution | 11 rules + 7b, each with "Prevents:" | 5 rules | v2 rule 10 (two failed fixes, then stop) and rule 6 (state what's unverified) are real beginner problems. Put them in **Watch for**, not as prompts. |
| Spec | 10 concrete items, including Out of scope | 5 drafting prompts | Coverage is fine. Spec 5 (must / won't) needs the user's input. |
| Tech | Specific stack, threading, modules, limitations | 5 prompts | Coverage is fine. v2's detail came from building, so the starter can't know it in advance. |
| Tasks | Ordered 0–6 + a "keep going / stop only for…" clause | 5 prompts | Nothing tells Claude **when to keep going and when to stop and ask** during the build. See R5. |

---

## 3. Recommendations

### R1. Start with an app brief, `idea.md`

Before anything else, the user fills in a five-line template (section 4) and saves it in the app folder as `idea.md`. The brief covers:

- what the app does
- the platforms it's built and run on
- its features
- what the user enters or sets
- what the screen shows

It gives every prompt its context. "Check tools" knows which platforms; the Spec drafts from the user's own words and features. It also **removes every [bracket] from the prompts**, since all the fill-ins now live in `idea.md`.

### R2. Label every line RULE, DRAFT, USER INPUT or PASTE

- **RULE, DRAFT and USER INPUT** are part of the pasted text, so Claude reads them. The user pastes the legend once, at the start, so Claude knows what each label means.
- **PASTE** marks a plain instruction line that has no category (setup, save, build).
- **Labels are in capitals** so the user can find them quickly and nothing else is emphasized.

### R3. Paste the Constitution as one block

All five Constitution lines are RULEs. Pasting them together removes the risk that Claude acts on them early. Spec, Tech and Tasks stay one line at a time, because those are back-and-forth.

### R4. Separate what you paste from what you read

- Every paste-able line goes in a code box.
- Everything else is plain text, for the reader.
- No brackets remain in any prompt (R1).

### R5. Tell the builder when to stop, inside the build line

**The user does nothing extra.** The build starts with the last PASTE line in "After you finish", which the user pastes into a fresh Claude Code session. That line gets one more sentence:

> PASTE: Build from masterplan.md, starting with Task 1. **Keep going unless a test fails or you need my decision.**

- **Without that sentence,** Claude either stops after every step to ask "shall I continue?" or keeps going past a failure.
- **With it,** the stop conditions are set once, where the build begins.

### R6. Sharper "Watch for" bullets

- **"Looks close" is not a pass.** Eyeballing a screen is not a test. Ask for the measured pass/fail output.
- **Two failed fixes on the same bug:** stop, and ask Claude what it measured. *(from v2 rule 10)*
- **"Should work" means untested:** ask Claude for proof. *(from v2 rule 6)*
- **When a data source fails,** ask Claude for likely causes and fixes. *(moved out of Spec 4)*

Total: 20 prompts, still 5 per section and ≤20 words each (labels not counted).

- RULE: 9
- DRAFT: 9
- USER INPUT: 2

---

## 4. Proposed revised starter

# Masterplan Starter: 20 Prompts for Spec-Driven Development

You write a short app brief, then paste the prompts below into Claude Code. Claude builds `masterplan.md` from them: a short outline of your app that becomes its source of truth.

**Label key** *(for you to read; don't paste)*

- **RULE:** Claude writes it into the masterplan and follows it during the build, not now.
- **DRAFT:** Claude writes that part of the masterplan now, and shows it to you for review.
- **USER INPUT:** Claude asks you first, then writes down your answer.
- **PASTE:** a plain instruction to paste as is.

### Before you start

1. **Make an empty folder for your app.** In it, create a text file named `idea.md`, and fill in these five lines:

   ```
   My app [does what] for [who]. It's like [example app, if any], except [what's different].
   I'll build it on [Mac/Windows/Linux]; it must also run on [Mac/Windows/Linux].
   Main features, most important first: [feature 1], [feature 2], [feature 3].
   The user enters or sets: [inputs and settings, with typical values or ranges].
   The screen shows: [displayed items]; its data comes from [a website, a file, a radio…].
   ```

2. **Add a screenshot** of an app like the one you want, in the same folder.
3. **Install Claude Code, and open a terminal:**
   - Mac: Terminal
   - Windows: PowerShell
   - Linux: your terminal

   Go to your folder, and type `claude`.
4. **Paste the setup line:**

   ```
   PASTE: Create masterplan.md with four sections: Constitution, Spec, Tech, Tasks. Read idea.md and my screenshot first.
   ```

5. **Paste the legend,** once:

   ```
   PASTE: Each line I send starts with a label. RULE: add it to masterplan.md as written; follow it during the build, not now. DRAFT: write that part of masterplan.md, then show me for review. USER INPUT: ask me first, then write my answer into masterplan.md.
   ```

### 1. Constitution: how we work *(paste all five at once)*

```
RULE: Build from masterplan.md, idea.md and my screenshot; borrow language, tools, specs, or open-source code from examples as I choose.
RULE: At the start of the build, check tools, libraries, GitHub login, and this folder's repo on my platforms; show pass/fail.
RULE: After each change, measure the app against the Spec's screen list; show pass/fail.
RULE: After each working step: run all tests, show me proof, commit, and push to GitHub.
RULE: When code and masterplan disagree, propose only major changes, one line each; update the masterplan after I approve.
```

### 2. Spec: what the app does *(one at a time)*

```
DRAFT: Summarize what my app does in a few sentences, from idea.md, my screenshot, and any example app.
DRAFT: From my screenshot, list screen elements and controls, with where each sits.
DRAFT: For each feature in idea.md, describe what the user does and sees, with testable ranges where possible.
DRAFT: List where my app's data comes from, and how we'll check each source works.
USER INPUT: Ask which features from idea.md my app must do this iteration, and which wait; suggest if I'm unsure.
```

### 3. Tech: what it's built with *(one at a time)*

```
USER INPUT: Recommend a language and tools, Python unless my example suggests better; explain each and let me choose.
DRAFT: List what must be installed on each platform in idea.md, and how to check each works.
RULE: Keep the app responsive while it works, never stuck waiting on data or input.
DRAFT: Propose a handful of well-structured modules, one line each, each testable on its own.
RULE: Keep a short list of known limitations in the masterplan; update it as we go.
```

### 4. Tasks: in what order *(one at a time)*

```
DRAFT: Break the build into small ordered steps, starting with setup and ending with a working app.
DRAFT: For each step, say how we'll test it and what result means it works.
RULE: Test connections to outside data with real servers before building screens that depend on them.
RULE: Get a simple version running early, then add features one at a time, testing each.
DRAFT: Add a final step: review what went wrong and propose masterplan updates for my approval.
```

### After you finish

1. **Save:**

   ```
   PASTE: Save everything into masterplan.md, keeping each section a short outline.
   ```

2. **Read masterplan.md yourself,** and fix anything that doesn't match what you want.
3. **Pressure-test:**

   ```
   PASTE: Pressure-test masterplan.md for ambiguity; list unclear items with one-line fixes for my approval.
   ```

4. **Save to GitHub:**

   ```
   PASTE: Create a private or public GitHub repo for this folder, as I choose; commit and push masterplan.md, idea.md and my screenshot.
   ```

5. **Build.** Quit Claude, start a new Claude Code session in the same folder, and paste:

   ```
   PASTE: Build from masterplan.md, starting with Task 1. Keep going unless a test fails or you need my decision.
   ```

### Watch for

- **"Looks close" is not a pass.** Eyeballing a screen is not a test. Ask for the measured pass/fail output.
- **Two failed fixes on the same bug:** stop, and ask Claude what it measured.
- **"Should work" means untested.** Ask for proof.
- **When a data source fails,** ask Claude for likely causes and fixes.
- **Tests:** never let Claude change a test just to make it pass.
- **Borrowed code:** note its license in the commit. GPL code makes your whole app GPL.
- **GitHub:** your app's folder needs its own repo. A folder inside another repo commits to the parent.
- **Keep the masterplan short:** it's an outline of what you want, not a record of every detail.

---

## 5. Line-by-line changes (before → after)

| # | Before | After | Why |
|---|---|---|---|
| C1 | …masterplan.md and my screenshot… | …masterplan.md, idea.md and my screenshot… | Uses the brief |
| C2 | Before any code, check my tools, libraries, GitHub login, and this folder's own repo; give me a pass/fail list. | At the start of the build, check tools, libraries, GitHub login, and this folder's repo on my platforms; show pass/fail. | Timing is explicit; platforms come from idea.md |
| C3 | …measure our app… | …measure the app… | Minor |
| S1 | Describe what my app does…, using [example app] and my screenshot as guides. | Summarize…, from idea.md, my screenshot, and any example app. | No brackets |
| S2 | …; I'll confirm or correct it. | …, with where each sits. | DRAFT already means review |
| S3 | For features, describe… | For each feature in idea.md, describe… | Features come from the brief |
| S4 | …; when one fails, show me likely causes and fixes. | …, and how we'll check each source works. | Failure help moved to Watch for |
| S5 | List what my app must do this iteration, and what it won't do. | USER INPUT: Ask which features from idea.md my app must do this iteration, and which wait; suggest if I'm unsure. | You decide, not Claude |
| T1 | Suggest a language and tools…; use Python unless… Explain each. | USER INPUT: Recommend…; explain each and let me choose. | You decide |
| T2 | I'm building on [Mac/Windows/Linux]; my app must also run on […]. | DRAFT: List what must be installed on each platform in idea.md, and how to check each works. | Platforms move to idea.md; gives C2 its checklist |
| T4 | Split the code into a handful of well-structured modules… | DRAFT: Propose a handful of well-structured modules, one line each… | Produces content now |
| K5 | Mark steps done only after tests pass; at the end, note what we learned… | DRAFT: Add a final step: review what went wrong and propose masterplan updates… | "Tests pass" is already C4 |
| Build | Build from masterplan.md, starting with Task 1. | …Keep going unless a test fails or you need my decision. | Sets the stop conditions once (R5) |

---

## 6. Adoption to-dos

Moved to the punch list (`notes/punch-list-sept29.md`, section "Masterplan Generator").
