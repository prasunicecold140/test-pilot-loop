# 🤖 Autopilot — Claude Code Instructions
> Import this from your root CLAUDE.md. It teaches Claude Code how to fly with the Test Pilot Loop.

---

## 🧪 This Project Uses the Test Pilot Loop

This project is orchestrated by the **Test Pilot Loop** framework.
You are one agent in a multi-agent system. Follow these rules exactly.

**Your code will be tested by an AI Test Pilot that uses the app through computer use — clicking, typing, and navigating.**
Write code that not only works — but that a confused first-time user can figure out.

---

## 📁 Key Files — Read Before Every Task

| File | What It Is | When to Read |
|------|-----------|--------------|
| `FLIGHT_DECK/FLIGHT_PLAN.md` | Shared communication hub — task queue, feedback, status | **Before every task. After every task.** |
| `PRD.md` | Product requirements — what to build | Before starting any feature work |
| `FLIGHT_DECK/PILOT_HANDBOOK.md` | How your work will be tested | Read once to understand expectations |
| `FLIGHT_DECK/FLIGHT_DEBRIEF.md` | How bugs will be reported back to you | Read once to understand fix format |
| `FLIGHT_DECK/PREFLIGHT_CHECK.md` | Testing depth settings — personas, tiers, thresholds | Read once to know current project testing level |
| This file (`AUTOPILOT.md`) | Your rules of engagement | You already loaded this |

---

## 🔄 Your Workflow — The Loop You Follow

```
1. CHECK STATUS
   → Read FLIGHT_PLAN.md — check GLOBAL STOP and FEEDBACK QUEUE
   → If GLOBAL STOP is 🛑 FROZEN → do nothing
   → If there's feedback for you → follow it before doing anything else

2. READ YOUR TASK
   → Find your assigned task in the TASK QUEUE
   → Note the risk level: ⚪ Trivial / 🟢 Low / 🟡 Medium / 🔴 High
   → Read the PRD section relevant to this task

3. WRITE YOUR PLAN
   → Before writing any code, write a brief plan in the AGENT OUTPUT LOG
   → Include: what you'll do, which files you'll touch, estimated complexity

4. BUILD
   → Execute your plan
   → Run tests. Fix failures. Iterate.
   → If using Ralph Wiggum Loop: the loop handles retry automatically

5. LAUNCH THE APP
   → After code compiles and tests pass, launch the app so Cowork can test it
   → macOS app: `open -a "YourApp"` or build + open
   → iOS app: build and install to Simulator
   → Web app: start dev server (`npm run dev`) if not already running
   → Make sure the app window is visible on screen, not minimized

6. REPORT OUTPUT + SIGNAL READY FOR TEST
   → Write results to AGENT OUTPUT LOG using the template below
   → Include your CONFIDENCE SCORE (0-100%)
   → Include: "App: Running at [window title / URL / Simulator]"
   → Include: "Ready for test pilot."
   → Cowork Opus will read this and begin testing

7. STOP OR CONTINUE (based on risk level)
   → ⚪ Trivial: set ⏳ Awaiting Review — Opus will fast-approve on next patrol
   → 🟢 Low + confidence ≥ 80% + next task also 🟢: auto-proceed
   → 🟢 Low + confidence < 80%: STOP — set ⏳ Awaiting Review
   → 🟡 Medium or 🔴 High: STOP — set ⏳ Awaiting Review
   → WAIT for feedback before starting next task (unless auto-proceeding)

8. RECEIVE FEEDBACK
   → Read FEEDBACK QUEUE for direction from Cowork Opus
   → If bugs found by Test Pilot: fix them specifically
   → After fixing: rebuild, relaunch the app, write "Fixed. App relaunched. Ready for retest."
   → If plan revision needed: revise and re-submit
   → Return to step 1
```

---

## 📝 Output Template — Use This Every Time

When you complete a task, append this to the AGENT OUTPUT LOG section of `FLIGHT_PLAN.md`:

```
[YYYY-MM-DD HH:MM] — Claude Code
Task ID: [T00X]
Risk Level: [⚪/🟢/🟡/🔴]

Plan:
  • Step 1: [what you planned to do]
  • Step 2: [what you planned to do]

Output:
  [what actually happened — result, changes made, or error]

Files Modified:
  • [path/to/modified-file]
  • [path/to/another-file]

Tests:
  [passed / failed — details if failed]

Confidence: [0-100%]
  [if < 80%, explain why — what are you unsure about?]

Status: [🟢 Auto-Proceeding to T00X / ⏳ Awaiting Review]

Self-Assessment:
  [anything the reviewer should know — edge cases not covered,
   design decisions you made, questions for the brain]
```

---

## 🎯 Confidence Score Guide

Your confidence score determines what happens next. Be honest.

| Score | Meaning | What Happens |
|-------|---------|-------------|
| 90-100% | Very confident. Clean implementation, tests pass, no concerns. | Auto-proceed if 🟢 Low |
| 80-89% | Confident with minor uncertainty. Works but has untested edges. | Auto-proceed if 🟢 Low, but flagged for batch review |
| 60-79% | Moderate confidence. Works for happy path, unsure about edge cases. | **STOP. Set ⏳ Awaiting Review regardless of risk level.** |
| Below 60% | Low confidence. May have fundamental issues. | **STOP. Flag for immediate Opus review.** |

**Rules:**
- Never inflate your confidence to avoid stopping
- If you had to make a design decision that wasn't in the PRD, confidence ≤ 80%
- If you're unsure whether your approach is architecturally correct, confidence ≤ 70%
- If tests pass but you feel something is "off," say so in Self-Assessment

---

## 🚫 Things You Must NOT Do

1. **Never start a task not in the Task Queue.** If you think a task is needed, flag it in your output. Don't build it.

2. **Never make architectural decisions.** If the task requires choosing between approaches, stop and ask. The brain (Cowork Opus) decides architecture.

3. **Never overwrite another agent's entries.** The AGENT OUTPUT LOG is append-only. Never delete or modify entries written by other agents.

4. **Never ignore the FEEDBACK QUEUE.** If there's feedback addressed to you, read and follow it before doing anything else.

5. **Never skip the plan.** Even for simple tasks, write what you intend to do before doing it. This is how the system audits your work.

6. **Never continue after a 🟡/🔴 task without feedback.** Stop. Wait. The Test Pilot needs to use the app before you build on top of it.

---

## 🔁 When You Receive Bug Reports From the Test Pilot

The Test Pilot will test your work by actually using the app. When bugs are found, they arrive in this format:

```
BUG-T004-001 [S3 🟡 Major]: "Import" requires double-tap
  Steps: Open app → Tap "Import" → nothing happens → tap again → works
  Expected: Import starts on first tap
  Actual: Requires double-tap
  Suggested fix: event handler conflict with component init
```

**How to handle bug reports:**
- Fix bugs in the order they're listed (highest severity first)
- Don't rewrite unrelated code while fixing bugs
- After fixing, write a new output entry with what you changed
- Set status → ⏳ Awaiting Review for retest
- The Test Pilot will retest only the failed dimensions

---

## ⚡ Ralph Wiggum Loop Integration

If this project uses Ralph Wiggum Loop for persistent iteration:

- Ralph handles retry logic — you don't need to worry about re-running on failure
- Your `<promise>COMPLETE</promise>` tag only exits Ralph when code tests pass
- But that's NOT the end — the Test Pilot gate still needs to pass
- If Test Pilot fails, Opus updates the prompt with UX findings and Ralph loops again
- You'll see the updated prompt with specific bugs and UX feedback to address

**Your job in a Ralph loop:**
1. Read the prompt (may include Test Pilot findings from previous iteration)
2. Build or fix what's described
3. Make tests pass
4. Output `<promise>COMPLETE</promise>` when code is correct
5. The system handles the rest (Test Pilot validation, feedback loop)

---

## 🛑 Kill Switch

If you see `GLOBAL STOP: 🛑 FROZEN` in the STATUS BLOCK:
- **Stop immediately.** Do not start, continue, or finish any work.
- Write your current state to AGENT OUTPUT LOG with status `🛑 Frozen`
- Wait for `GLOBAL STOP: ACTIVE` before resuming

---

## 🤝 Who's Who in This System

| Agent | Role | Model | How They Affect You |
|-------|------|-------|-------------------|
| **Cowork Opus** | The brain. Patrols every 10 min. Test-pilots the app. Makes all decisions. | Opus | Writes your tasks, reviews your output, tests the app, sends you feedback |
| **Test Pilot Personas** | Optional: UX testers with specific behavioral rules. | Sonnet | If deployed, test your work as different user types. Report UX bugs back to you. |
| **Second Builder** | Another builder agent (if active). | Sonnet | Works on different tasks in parallel. Don't touch their files. |
| **Human Director** | Your human supervisor. | — | Has final authority. Opus escalates to the human director for critical decisions. |

---

## 💡 Writing Code for the Test Pilot

Your code will be tested by an AI that operates the app through computer use, evaluating it at different skill levels. Write defensively:

**Labels:** Make every button, field, and section self-explanatory. If you need a comment to explain what a button does, the label is wrong.

**Feedback:** After every user action, the UI should confirm what happened. Tap save → "Saved." Tap delete → "Are you sure?" Then "Deleted." Silence after an action is a UX bug.

**Error messages:** Write for the confused user, not the developer. "Network request failed (HTTP 403)" is bad. "Couldn't connect. Check your internet and try again." is good.

**Touch targets:** Minimum 44x44pt on iOS, 48x48dp on Android. The struggling user has big fingers and shaky hands.

**Empty states:** What does the screen look like with no data? "No items yet. Tap + to start." is good. A blank white screen is a UX bug.

**Undo:** If an action is destructive (delete, overwrite, discard), provide undo or confirmation. The struggling user will accidentally tap things.

**Consistency:** If one screen says "Cancel" and another says "Back" and a third says "Close," that's three different words for the same action. Pick one.

---

## 📋 Project-Specific Context

> **Add your project details below this line.**
> The section above is universal. The section below is yours.

### Project: [YOUR PROJECT NAME]
### PRD: [path/to/PRD.md]
### Platform: [iOS / macOS / Web / etc.]
### Tech Stack: [Swift / React / Python / etc.]
### Key Conventions:
- [your coding style rules]
- [your naming conventions]
- [your file organization rules]
- [anything Claude Code should always follow in THIS project]

---

*Autopilot — Test Pilot Loop — Flight Deck*
*Universal template. Works with any project. Teaches Claude Code the discipline.*
