# Getting Started with Test Pilot Loop

## What You Need

- A project with a spec (`CLAUDE.md` or `PRD.md` describing what the app does)
- **Claude Code** (or Codex CLI, or any AI coding agent) — this is your builder
- **Cowork** (Claude desktop app with computer use) — this is your test pilot

## Setup — Two Steps

### 1. Tell your builder agent:

> "Read `FLIGHT_DECK/SETUP.md` from the Test Pilot Loop framework and set up the test pilot loop for this project."

The builder reads the framework, reads your project spec, asks you a few questions about your project (build tool, test data, what you're worried about), then generates all the configuration files automatically: `FLIGHT_PLAN.md`, permissions, `INSIDER_GOALS.md`, patrol watcher — everything.

### 2. Tell Cowork:

> "Read `FLIGHT_DECK/SETUP.md` and set up as test pilot. Start testing."

Cowork reads the framework, reads your project spec, pre-approves app access (you click "Allow" once), sets up patrol (manual or scheduled task), and confirms it's ready.

That's it. The loop starts when both agents confirm in `FLIGHT_PLAN.md`.

## What Happens Next

```
Builder builds feature → writes "Ready for test" to FLIGHT_PLAN.md
  ↓
Test Pilot sees it → launches app → tests it through computer use
  ↓
Test Pilot writes feedback + bugs to FLIGHT_PLAN.md
  ↓
Builder reads feedback → fixes bugs → writes "Ready for retest"
  ↓
Test Pilot retests → loop until it passes
  ↓
✅ Ship it
```

You can watch the loop in `FLIGHT_DECK/FLIGHT_PLAN.md` — it's a plain markdown file that both agents write to. You can also intervene at any time by editing the file or talking to either agent.

## Your Controls

- **Kill switch:** Write `GLOBAL_STOP: FROZEN` in the Status Block of FLIGHT_PLAN.md — both agents halt immediately
- **Resume:** Change it back to `GLOBAL_STOP: ACTIVE`
- **Add tasks:** Write new tasks in the TASK QUEUE section
- **Override:** Talk to either agent directly — they'll follow your instructions over anything in the file

## Learn More

- `TEST_PILOT_LOOP.md` — full framework documentation
- `QUICK_FLIGHT.md` — lighter version, zero setup, 5-minute test
- `README.md` — project overview and all file descriptions
