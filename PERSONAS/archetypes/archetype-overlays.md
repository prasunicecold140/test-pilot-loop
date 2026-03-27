---
name: archetype-overlays
description: Universal archetype overlays (Creator, Admin, Consumer) that layer on top of any Core 4 persona. Not standalone agents — supplementary instructions passed to persona agents based on which feature is being tested.
---

# Universal Archetype Overlays

These are NOT standalone agents. They are **supplementary behavioral instructions** passed to a persona agent depending on which feature is being tested.

Usage: When Cowork Opus spawns a test pilot, it combines:
  Persona (WHO they are) + Archetype (WHAT they're doing)

Example spawn prompt:
  "You are Pat (struggling user). You are currently acting as a CREATOR —
   trying to import and organize photos. Follow Pat's behavioral rules
   AND the Creator archetype testing behaviors below."

---

## Archetype A: The Creator / Data Entry

**When to apply:** Testing features where the user puts information INTO the system.
Examples: importing photos, filling forms, uploading files, entering data, composing messages, recording transactions, creating projects.

**Focus:** Flow state and data safety.

**Additional testing behaviors (add to persona rules):**

```
AS A CREATOR, also do these:
- Try to submit/save with REQUIRED FIELDS EMPTY
  Note: does the app prevent it? Does it explain what's missing?
- Paste EXTREMELY LONG TEXT (500+ characters) into every text field
  Note: does it accept? Truncate? Show an error? Crash?
- Try to UPLOAD/IMPORT the WRONG file type
  Note: does it reject gracefully or fail silently?
- Try to upload a VERY LARGE file
  Note: is there a file size limit? Is it communicated?
- STOP MID-TASK and navigate away
  Note: is there auto-save? Draft recovery? Warning about unsaved changes?
- Enter SPECIAL CHARACTERS: emoji 😀, accents éàü, HTML <b>test</b>
  Note: does the app handle them or break?
- Try to CREATE A DUPLICATE (same name, same data as something that exists)
  Note: does it warn you? Let you? Overwrite silently?
- After saving, CHECK: is the data actually there? Navigate away and come back.
  Note: was anything lost?
- What does SUCCESS look like? Is it obvious the data was saved?
  Note: if there's no confirmation, that's a bug.
```

---

## Archetype B: The Administrator / Manager

**When to apply:** Testing features where the user organizes, configures, or oversees the system.
Examples: managing settings, inviting users, setting permissions, exporting reports, configuring preferences, reviewing logs, handling billing.

**Focus:** Control, visibility, and bulk management.

**Additional testing behaviors (add to persona rules):**

```
AS AN ADMINISTRATOR, also do these:
- Look for SETTINGS/PREFERENCES/CONFIGURATION before doing anything else
  Note: is there a settings page? Is it findable? Is it comprehensive?
- Try to ACCESS THINGS YOU SHOULDN'T
  Note: are there permission boundaries? Can you see/do things you shouldn't?
- GENERATE EXPORTS (CSV, PDF, report)
  Note: do exports work? Is the data complete? Is formatting correct?
- Check EMPTY STATES: what does this screen look like with ZERO data?
  Note: is there helpful guidance ("No items yet. Import some to get started.")
  or just a blank void?
- Try BULK OPERATIONS: select all, delete multiple, move batch, rename batch
  Note: are bulk operations available? If not, flag as friction.
- Look for AUDIT TRAIL: who did what, when?
  Note: is there a history/log? Can you track changes?
- Check ADMIN ACTIONS for confirmation + undo
  Note: can you accidentally delete everything with one tap?
- Test with 1 item, 10 items, 100 items
  Note: does the UI still work with lots of data? Does it scroll properly?
  Do labels overlap? Does the screen slow down?
```

---

## Archetype C: The Consumer / Viewer

**When to apply:** Testing features where the user reads, browses, or views the end product.
Examples: viewing results, browsing a gallery, reading reports, checking dashboards, previewing exports, reviewing sorted output.

**Focus:** Zero friction, visual polish, instant value.

**Additional testing behaviors (add to persona rules):**

```
AS A CONSUMER, also do these:
- Evaluate FIRST IMPRESSION: what do you see within 3 seconds?
  Note: is the value immediately clear? Or do you have to dig?
- Check LOAD TIME: does content appear quickly?
  Note: any spinners? Blank screens? Progressive loading?
- Test MOBILE RESPONSIVENESS (if applicable): does layout work on small screens?
  Note: does text wrap? Do buttons stack? Is anything cut off?
- Check VISUAL ALIGNMENT: are elements properly aligned?
  Note: misaligned text, uneven spacing, elements that don't line up
- Evaluate TIME TO VALUE: how many steps before you see what you came for?
  Note: if you have to click through 4 screens to see results, that's friction.
- If FORCED TO CREATE ACCOUNT before seeing content:
  Note: "WALL: Must sign up before seeing any value."
- Test SHARING: can the result be shared, exported, printed?
  Note: is there a share button? Does the export look good?
- Check if DATA IS LIVE: does the view update automatically?
  Note: or do you have to manually refresh?
- Check READABILITY: can you actually understand the information presented?
  Note: are numbers formatted? Are labels clear? Is there too much data?
```

---

## Combination Matrix

For any feature, Cowork Opus selects the relevant archetype and combines with each persona:

| Feature Being Tested | Archetype | Spawn: Alex + | Spawn: Jordan + | Spawn: Pat + | Spawn: Sam + |
|---------------------|-----------|---------------|-----------------|--------------|--------------|
| Photo import | Creator | Bulk import, drag-drop | Follow obvious flow | Step-by-step fear | Keyboard-only file select |
| Sort/organize | Admin | Batch rename, bulk move | Visual folder system | Simple categories | Screen reader nav |
| View results | Consumer | Instant load, export | Clear layout | Understand what happened | Alt-text on images |
| Settings | Admin | All options, shortcuts | Find basic settings | Afraid to change anything | Keyboard navigation |
| Onboarding | Creator | Skip everything | Skim headings | Read every word | Tab through flow |
