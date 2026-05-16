# Contributing to questforge-data

Thank you for helping build the QuestForge quest library. Every quest file you contribute makes the plugin more useful for the whole FFXIV community.

Quest definitions in this repo are licensed [CC-BY-4.0](LICENSE) — you keep attribution rights to your contributions.

---

## What this repo contains

- `quests/` — Quest definition files in JSON format, one file per quest
- `fragments/` — Reusable step sequences shared across multiple quests
- `fixtures/engine/` — Engine test fixtures used for regression testing

---

## Prerequisites

- A working FFXIV installation with [Dalamud](https://goatcorp.github.io/) installed
- The QuestForge plugin installed and working
- A git client

No programming experience required. The authoring mode (see below) handles all the JSON for you.

---

## The two ways to contribute

### 1. Authoring mode (recommended)

The easiest path. No JSON editing required.

1. Log in to FFXIV with the QuestForge plugin active
2. Navigate to the quest you want to record
3. Run `/qf author <questId>` (use `/qf debug quest <id>` to look up a quest ID by name)
4. Play through the quest normally — after each step, click **Record** in the authoring panel
5. When the quest is complete, click **Export** — this writes the quest file to your local `questforge-data` directory
6. Open a pull request with the exported file

### 2. Manual editing

Write or edit the JSON directly. Use this approach for corrections, filling in missing fields, or working on quests that are difficult to record.

- See `docs/SCHEMA.md` in the [questforge](https://github.com/simplekantan/questforge) repo for the full format reference
- Validate your file locally before submitting: `dotnet run --project questforge-tools/qf-validate -- .` (requires [questforge-tools](https://github.com/simplekantan/questforge-tools) checked out as a sibling)

---

## Quest file location

Files live at `quests/<expansion>/<category>/<questId>-<slug>.json`.

Example: `quests/arr/msq/66130-coming-to-uldah.json`

**Expansions:** `arr`, `heavensward`, `stormblood`, `shadowbringers`, `endwalker`, `dawntrail`

**Categories:** `msq`, `class`, `job`, `role`, `blue`, `side`

The slug is the quest name lowercased with spaces replaced by hyphens. Authoring mode sets the file path automatically.

---

## Validation

**Quest files** — CI runs `qf-validate` automatically on every PR that touches `quests/` or `fragments/`. Errors appear inline in the PR checks.

Run locally before pushing (requires `questforge-tools` submodule initialised):

```bash
dotnet run --project questforge-tools/qf-validate -- .
```

**Fixture files** — validate a fixture against its referenced quest file using `qf-trace`:

```bash
qf-trace validate-fixture fixtures/engine/simple-linear-acceptance.json --quest-data .
```

Or build and run directly if `qf-trace` is not on your PATH:

```bash
dotnet run --project questforge-tools/qf-trace -- validate-fixture fixtures/engine/simple-linear-acceptance.json --quest-data .
```

---

## What makes a good quest file

- Tested in-game with QuestForge automation running — the quest completes without manual intervention
- Correct step IDs matching the in-game sequence
- All `expect` predicates filled in (not left as placeholders)
- NPC coordinates accurate enough that pathfinding reliably reaches the target
- `lastVerifiedPatch` set to the current patch version (e.g. `"7.2"`)

---

## Submitting a PR

- One quest per PR is preferred — it keeps review focused
- Use the quest name and ID in the PR title: e.g. `Add quest: Coming to Ul'dah (66130)`
- In the PR description, note anything unusual about the quest: mid-quest cutscenes, dialogue branches, class-specific steps, escort sequences, or anything that required special handling

---

## After your PR merges

The quest becomes available to all QuestForge users on the next plugin data refresh. Your name will appear in the file's `author` field and in the git history.

---

## Questions

Open an issue in this repo, or ask in the [Dalamud Discord](https://discord.gg/holdingcontrol) `#plugin-help` channel.