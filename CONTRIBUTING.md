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

## Predicate syntax

Predicates appear in `expect` and `skipIf` fields. They are JSON strings written using a small expression language.

**Predicate functions** — all predicates are function calls, even zero-argument ones:

| Predicate | Example | True when |
|---|---|---|
| `questSequence(questId) >= N` | `"questSequence(66130) >= 1"` | Quest journal sequence is at least N |
| `isQuestComplete(questId)` | `"isQuestComplete(66130)"` | Quest is in the completed state |
| `isQuestAccepted(questId)` | `"isQuestAccepted(66130)"` | Quest is currently active |
| `questFlag(questId, flag)` | `"questFlag(66130, 1)"` | A specific quest flag bit is set |
| `playerZone() == zoneId` | `"playerZone() == 182"` | Player is in the given zone |
| `playerNear(pos, radius)` | `"playerNear({x:35.5,y:4.0,z:-151.2}, 3)"` | Player is within radius of the position |
| `playerInCombat()` | `"playerInCombat()"` | Player is currently in combat |
| `playerHasItem(itemId)` | `"playerHasItem(2000386)"` | Player's key-item inventory contains the item |
| `isAttuned(aetheryteId)` | `"isAttuned(9)"` | Player is attuned to the aetheryte |

**Logical operators** — `and`, `or`, `not` are infix/prefix keywords inside the predicate string:

```
"questSequence(66130) >= 1 and playerZone() == 182"
"not playerInCombat()"
"isQuestComplete(66130) or playerHasItem(2000386)"
```

**Multi-predicate `expect`** — when you need all or any of a list, use the schema `all`/`any` JSON forms instead of a predicate string:

```json
"expect": { "all": ["playerNear({x:35.5,y:4.0,z:-151.2}, 3)", "playerZone() == 182"] }
"expect": { "any": ["isQuestComplete(66130)", "questSequence(66130) >= 5"] }
```

> **Note:** The predicates listed above are implemented in the current engine runtime. The full schema specification (`docs/SCHEMA.md`) documents additional functions that are planned but not yet evaluated at runtime — using unimplemented functions will cause a runtime error.

---

## Step fields: `expect` and `skipIf`

Most step types support two optional guard fields:

- **`expect`** — a predicate that must be true when the step finishes. If the postcondition check fails, the engine counts a step failure and retries. Use this to declare what "done" looks like for a step (e.g. `"expect": "questSequence(66130) >= 1"` after a `talk` step).

- **`skipIf`** — a predicate evaluated before the step runs. If it evaluates to true the step is skipped entirely and the engine advances. Use this when a step may already be complete on re-entry (e.g. skip an `attune` step with `"skipIf": "isAttuned(9)"`).

Both fields accept the same predicate syntax described above.

---

## Step type reference: `attune` and `hand-over-item`

Two step types added in the Phase 11 engine that are not covered by authoring mode's automatic detection — you may need to add them manually when editing quest files.

### `attune`

Use when the quest requires the player to attune to an aetheryte, aethernet shard, or aetherial node.

```json
{
  "type": "attune",
  "id": "attune-to-ul-dah-aetheryte",
  "target": { "objectId": 2000401, "position": { "x": -158.0, "y": 16.0, "z": 83.0 }, "zone": 130 },
  "expect": "isAttuned(70)"
}
```

`expect` should use `isAttuned(id)` so the engine can verify the attunement completed before moving on. `skipIf` with `isAttuned(id)` is recommended so repeated runs skip the step if already attuned.

### `hand-over-item`

Use when a quest step requires delivering a specific item to an NPC (distinct from the quest `turn-in` step, which completes the quest). This step is common in delivery-style objectives mid-quest.

```json
{
  "type": "hand-over-item",
  "id": "deliver-parcel-to-momodi",
  "npcId": 1006004,
  "position": { "x": -147.3, "y": 16.0, "z": 26.2 },
  "zone": 130,
  "itemId": 5368,
  "expect": "questSequence"
}
```

The engine navigates to the NPC, opens dialogue, and selects the item hand-over option. After completion it verifies `expect` — typically `questSequence` advancing to the next journal marker.

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