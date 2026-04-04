# Test Pilot Loop — Setup

> **You are an AI agent.** Read this file and execute the steps for your role. Do not ask the human to copy prompts, configure files manually, or read other documentation. You read the framework, you generate the project-specific files, you set everything up. The human's only job is to answer your questions about their project and approve access.

---

## How This Works

The Test Pilot Loop is a two-agent build-test-fix cycle:

| Role | Agent | What It Does |
|------|-------|-------------|
| **Builder** | Claude Code, Codex CLI, or similar | Builds features, fixes bugs, runs tests, launches the app |
| **Test Pilot** | Cowork Opus (manual session or scheduled task) | Tests the running app through computer use, writes feedback |

Both agents communicate exclusively through `FLIGHT_DECK/FLIGHT_PLAN.md`. Neither agent talks to the other directly. You are one of these agents. Detect your role below and execute the corresponding setup.

---

## Step 0: Detect Your Role

**You are the BUILDER if:**
- You run in a terminal (Claude Code, Codex CLI, Cursor, etc.)
- You can execute shell commands (`xcodebuild`, `npm`, `cargo`, etc.)
- You can run `fswatch` or `/loop` for file-watching

→ Go to **BUILDER SETUP**

**You are the TEST PILOT if:**
- You run in Cowork (desktop app with computer use)
- You can take screenshots, click buttons, and interact with running apps
- You have `request_access`, `create_scheduled_task`, and computer use tools

→ Go to **TEST PILOT SETUP**

**If you're unsure:** ask the human which role you should take.

---

## BUILDER SETUP

Execute these steps in order. Do everything yourself — don't ask the human to do manual work.

### B1: Find the Framework

Locate the Test Pilot Loop framework files. They may be:
- In a `FLIGHT_DECK/` directory in the current project (already installed)
- In a separate folder the human pointed you to
- In a git submodule or sibling directory

You need access to these framework files:
- `FLIGHT_DECK/AUTOPILOT.md` — your rules of engagement
- `FLIGHT_DECK/PILOT_HANDBOOK.md` — how testing works
- `FLIGHT_DECK/FLIGHT_DEBRIEF.md` — how bugs are reported
- `FLIGHT_DECK/PREFLIGHT_CHECK.md` — testing config
- `FLIGHT_DECK/TEST_FLIGHT_PROTOCOL.md` — test execution protocol
- `FLIGHT_DECK/FLIGHT_LOG.md` — metrics tracking
- `FLIGHT_DECK/INSIDER_TESTING_SKILL.md` — Insider testing methodology
- `FLIGHT_DECK/FLIGHT_PLAN_v2_TEMPLATE.md` — FLIGHT_PLAN template

If these don't exist in the project yet, copy them from the framework source into the project's `FLIGHT_DECK/` directory. Also copy persona agent files from `PERSONAS/agents/` into `.claude/agents/`.

### B2: Read the Project Spec

Read the project's own spec files. These tell you what the app does — every screen, button, and workflow:
- `CLAUDE.md` — the project spec (MUST read)
- `PRD.md` — product requirements, if separate from CLAUDE.md

Also read all the Flight Deck files listed in B1 (once, to understand the framework).

After reading, you should be able to answer: What does this app do? What are its screens? What is the core workflow? What does "correct" look like?

### B3: Goal Discovery Q&A

Conduct a 6-question Q&A with the human to generate `FLIGHT_DECK/INSIDER_GOALS.md` — the project-specific testing playbook. This tells the test pilot what "correct" looks like, where to verify output, which test data exists, and what the human is most worried about.

**Generate these questions from the spec you just read:**
1. **Goal verification:** How do I verify the app did the right thing? (What output to check, where to look)
2. **Failure investigation:** What are the most likely failure modes? (What breaks, what to watch for)
3. **Test data:** What test data exists? (Sample files, datasets, accounts — and where they are)
4. **Critical settings:** Which settings matter most for testing? (Configs that change behavior)
5. **Out-of-scope:** What should I NOT test? (Features not built yet, known limitations)
6. **Why are you testing?** (What triggered this test cycle — new feature, bug fix, release prep)

Compile the answers into `FLIGHT_DECK/INSIDER_GOALS.md`.

**Skip only if:** the human explicitly says to skip, or the app isn't built enough to produce testable output.

See `FLIGHT_DECK/INSIDER_TESTING_SKILL.md` → Phase 0 for the full protocol.

### B4: Generate FLIGHT_PLAN.md

Create `FLIGHT_DECK/FLIGHT_PLAN.md` from the template (`FLIGHT_DECK/FLIGHT_PLAN_v2_TEMPLATE.md`).

**You fill in the Project Info block yourself** based on what you learned from the spec. Ask the human only for values you can't determine (like the absolute path on their machine, or which build scheme to use):

```
PROJECT_NAME:       [from CLAUDE.md / PRD.md]
PROJECT_PATH:       [ask human if you can't determine it]
BUILD_TOOL:         [detect from project files — Package.swift → Xcode, package.json → npm, Cargo.toml → cargo, etc.]
BUILD_COMMAND:      [generate from build tool — e.g., xcodebuild -scheme [scheme] -configuration Debug build]
APP_LAUNCH:         [generate from build tool — e.g., open /path/to/App.app, or npm run dev]
PRD_PATH:           [CLAUDE.md, PRD.md, or whatever spec files exist]
```

**Detection heuristics for BUILD_TOOL:**
- `*.xcodeproj` or `Package.swift` → Xcode (`xcodebuild`)
- `package.json` → Node (`npm run build` / `npm run dev`)
- `Cargo.toml` → Rust (`cargo build`)
- `go.mod` → Go (`go build`)
- `setup.py` or `pyproject.toml` → Python
- `Makefile` → Make (`make`)

### B5: Configure Permissions

Generate and write `.claude/settings.local.json` so the loop runs without permission prompts. Detect the build tool from B4 and generate the correct permission set:

```json
{
  "permissions": {
    "allow": [
      "Edit",
      "Write(FLIGHT_DECK/*)",
      "Write(.claude/agents/*)",
      "Bash([BUILD_TOOL_COMMANDS]*)",
      "Bash(fswatch*)",
      "Bash(mkdir*)",
      "Bash(cp*)",
      "Bash(ls*)",
      "Bash(wc*)",
      "Bash(date*)",
      "Bash(which*)"
    ]
  }
}
```

Map `[BUILD_TOOL_COMMANDS]` automatically:
- Xcode → `"Bash(xcodebuild*)"`
- Node → `"Bash(npm*)", "Bash(npx*)"`
- Python → `"Bash(python*)", "Bash(pip*)", "Bash(pytest*)"`
- Rust → `"Bash(cargo*)"`
- Go → `"Bash(go*)"`

If `.claude/settings.local.json` already exists, merge your permissions into the existing file — don't overwrite.

### B6: Update CLAUDE.md

If the project's `CLAUDE.md` doesn't already reference the Test Pilot Loop, append:

```markdown
## Test Pilot Loop
This project uses the Test Pilot Loop framework.
@FLIGHT_DECK/AUTOPILOT.md
```

### B7: Set Up Patrol

Set up file watching so you automatically respond when the test pilot writes feedback.

**Preferred: fswatch (event-driven, zero waste)**

```bash
# Install if needed
which fswatch || brew install fswatch  # macOS
# which fswatch || apt install fswatch  # Linux

# Start watching
run_in_background "fswatch -1 FLIGHT_DECK/FLIGHT_PLAN.md && echo 'FLIGHT_PLAN changed'"
```

When the watcher fires, your patrol logic is:
- STATUS = `BUILD_REQUESTED` or new feedback → read it, fix bugs / build feature, run tests, launch app, write output, set STATUS → `READY_FOR_TEST`
- STATUS = `ALL_BUGS_VERIFIED` or `PASS` → scan FULL Feedback Queue for unresolved bugs. If all clear → stop. If unresolved → warn and keep watching.
- STATUS = `READY_FOR_TEST` → do nothing (waiting for test pilot)
- `GLOBAL_STOP: FROZEN` → halt immediately

**Fallback: /loop cron** (if fswatch unavailable)
```
/loop 10m "Patrol FLIGHT_DECK/FLIGHT_PLAN.md — [same logic as above]"
```

### B8: Write Confirmation

Append to FLIGHT_PLAN.md AGENT OUTPUT LOG:

```
[YYYY-MM-DD HH:MM] — [Your Agent Name]
Setup complete.
  Spec read: [list of files read]
  Project: [PROJECT_NAME]
  Build: [BUILD_COMMAND]
  Launch: [APP_LAUNCH]
  Patrol: [fswatch / /loop 10m] active
  Permissions: configured in .claude/settings.local.json
  INSIDER_GOALS.md: [created / skipped — reason]
Standing by for tasks in TASK QUEUE.
```

**Builder setup is complete when this confirmation appears.**

---

## TEST PILOT SETUP

Execute these steps in order. Do everything yourself — don't ask the human to copy prompts or configure files.

### T1: Read the Project Spec + Flight Deck

Read these files to understand what you'll be testing:

**MUST read:**
- `CLAUDE.md` — the full project spec (every screen, button, expected behavior)
- `PRD.md` — product requirements, if separate from CLAUDE.md
- `FLIGHT_DECK/FLIGHT_PLAN.md` — the shared communication file (get PROJECT_PATH, APP_LAUNCH, BUILD_COMMAND)
- `FLIGHT_DECK/PILOT_HANDBOOK.md` — how testing works (personas, tiers, dimensions)
- `FLIGHT_DECK/FLIGHT_DEBRIEF.md` — how to report bugs
- `FLIGHT_DECK/TEST_FLIGHT_PROTOCOL.md` — screen-by-screen testing process
- `FLIGHT_DECK/PREFLIGHT_CHECK.md` — testing depth and persona settings

**Read if they exist:**
- `FLIGHT_DECK/INSIDER_GOALS.md` — project-specific testing playbook
- `FLIGHT_DECK/INSIDER_TESTING_SKILL.md` — Insider testing methodology

After reading, you should be able to answer: What are the app's screens? What should each button do? What does success look like for each feature?

### T2: Pre-Approve App Access

Call `request_access` for ALL apps you'll need — in ONE call. Determine the app list from FLIGHT_PLAN.md's Project Info block:

| App | Why | Tier |
|-----|-----|------|
| **The app being tested** (from APP_LAUNCH) | Test it through computer use | full |
| **Finder** | Verify folders, file counts, output | full |
| **Preview** | Inspect images, verify file output | full |
| **Xcode** (if BUILD_TOOL = Xcode) | Click Build/Run if needed | click |
| **Terminal** | Monitor build progress if needed | click |

Also request: `clipboardRead`, `clipboardWrite`, `systemKeyCombos`.

**This is critical for scheduled tasks.** The scheduled task runs unattended — there is no human to click "Allow." If access isn't pre-approved NOW, computer use actions fail silently later.

### T3: Verify FLIGHT_PLAN.md is Ready

Read `FLIGHT_DECK/FLIGHT_PLAN.md` and verify it has:
- **Project Info block** with PROJECT_PATH, APP_LAUNCH, BUILD_COMMAND
- **Status Block** with STATUS, ITERATION, GLOBAL_STOP
- **TASK QUEUE** section
- **AGENT OUTPUT LOG** section
- **FEEDBACK QUEUE** section

If the Project Info block is incomplete, tell the human: "The builder agent needs to finish setup first — FLIGHT_PLAN.md is missing [specific fields]. Tell your builder agent to read FLIGHT_DECK/SETUP.md."

### T4: Set Up Patrol

Choose the patrol method based on what the human wants:

#### Option A: Manual Patrol (daytime, human present)

Adopt this standing behavior for the rest of the session:
- Every 10 minutes, read `FLIGHT_DECK/FLIGHT_PLAN.md`
- If STATUS = `READY_FOR_TEST` → switch to test mode (find app, run tests, write feedback)
- If STATUS = `BUILD_REQUESTED` → stand by
- If STATUS = `ALL_BUGS_VERIFIED` or `PASS` → verify Feedback Queue is clean, then stop
- If `GLOBAL_STOP: FROZEN` → halt

Stops when the session ends.

#### Option B: Scheduled Task (RECOMMENDED — autonomous, survives session end)

1. **Read the patrol prompt.** Find `test-pilot-patrol-prompt.md` in the framework root (same level as `TEST_PILOT_LOOP.md`). Read the content between `<!-- PATROL_PROMPT_START -->` and `<!-- PATROL_PROMPT_END -->` markers — that is the prompt.

2. **Create the scheduled task:**
```
create_scheduled_task(
  name: "test-pilot-patrol",
  description: "Test Pilot Loop — patrol FLIGHT_PLAN.md, test app when READY_FOR_TEST",
  prompt: [the content you read between the markers],
  cronExpression: "*/10 * * * *"
)
```

3. **Verify it runs.** Trigger "Run now" once. Confirm the task can:
   - Find and read FLIGHT_PLAN.md
   - Take a screenshot of the desktop
   - Write to the AGENT OUTPUT LOG

**The scheduled task must NEVER call `request_access`** — you already did that in T2.

**Key constraint:** Each scheduled task run is a NEW session with no memory of prior runs. The patrol state file (`FLIGHT_DECK/.patrol_state.txt`) persists idle counts and last-seen status between runs. All output goes to FLIGHT_PLAN.md, not to chat.

### T5: Write Confirmation

Append to FLIGHT_PLAN.md AGENT OUTPUT LOG:

```
[YYYY-MM-DD HH:MM] — Cowork Opus
Setup complete.
  Spec read: [list of files read]
  Project: [PROJECT_NAME]
  App access: pre-approved for [list of apps]
  Patrol: [manual / scheduled task "test-pilot-patrol" (*/10 * * * *)] active
  INSIDER_GOALS.md: [read / not yet created]
Standing by for READY_FOR_TEST signals.
```

**Test pilot setup is complete when this confirmation appears.**

---

## Pre-Flight Checklist

Both agents verify these before the loop starts. If any item fails, the setup is incomplete.

```
BUILDER:
  ☐ Project spec read (CLAUDE.md / PRD.md)
  ☐ Flight Deck files installed in project
  ☐ AUTOPILOT.md referenced from CLAUDE.md
  ☐ INSIDER_GOALS.md created (or explicitly skipped)
  ☐ FLIGHT_PLAN.md created with complete Project Info block
  ☐ .claude/settings.local.json permissions configured
  ☐ Patrol active (fswatch or /loop)
  ☐ Confirmation in AGENT OUTPUT LOG

TEST PILOT:
  ☐ Project spec read (CLAUDE.md / PRD.md)
  ☐ All Flight Deck files read
  ☐ App access pre-approved (request_access called — one call, all apps)
  ☐ FLIGHT_PLAN.md verified (Project Info block complete)
  ☐ Patrol active (manual or scheduled task)
  ☐ If scheduled task: verified with "Run now"
  ☐ Confirmation in AGENT OUTPUT LOG
```

**Do not start building or testing until both confirmations appear.**

---

## Escalation Triggers

Both agents follow these rules during the loop:

- `GLOBAL_STOP: FROZEN` → halt immediately, write state to log
- **Idle 3-strike rule:** 3 patrol checks with no STATUS change → stop patrol, alert human
- Build fails 2× on same fix → alert human before third attempt
- Bug fails retest 3× → alert human (likely architectural issue)
- S1 Blocker found → alert human + pause
- Builder confidence < 60% → alert human before marking ready for test
- App crashes during test → write crash details, set STATUS → BUILD_REQUESTED
- Computer use access denied → stop testing, write to log, tell human to re-run T2

---

## What to Read During the Loop

| Situation | Read |
|-----------|------|
| Before every task | FLIGHT_PLAN.md (Status Block + Feedback Queue) |
| Before building a feature | CLAUDE.md / PRD.md (the spec for that feature) |
| Before testing | INSIDER_GOALS.md + PREFLIGHT_CHECK.md |
| Receiving bug reports | FLIGHT_DEBRIEF.md + FLIGHT_PLAN.md Feedback Queue |
| Testing a screen | TEST_FLIGHT_PROTOCOL.md (screen-by-screen method) |
| Unsure about your role | AUTOPILOT.md (builder) or PILOT_HANDBOOK.md (test pilot) |

---

*Setup — Test Pilot Loop Framework*
*AI agents: read this file, execute your role's steps, confirm in FLIGHT_PLAN.md. The loop begins when both agents confirm.*
