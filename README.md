# questforge-data

Quest definitions and fragment library for [QuestForge](https://github.com/simplekantan/questforge).

[![Validate quest data](https://github.com/simplekantan/questforge-data/actions/workflows/validate.yml/badge.svg)](https://github.com/simplekantan/questforge-data/actions/workflows/validate.yml)
[![Quest Coverage](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/coverage-badge.json)](COVERAGE.md)
[![License: CC BY 4.0](https://img.shields.io/badge/license-CC--BY--4.0-lightgrey.svg)](LICENSE)

### Coverage by expansion

| Expansion | MSQ | Class | Role |
|-----------|-----|-------|------|
| **ARR** | [![ARR MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/arr-msq.json)](COVERAGE.md) | [![ARR Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/arr-class.json)](COVERAGE.md) | — |
| **Heavensward** | [![HW MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/heavensward-msq.json)](COVERAGE.md) | [![HW Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/heavensward-class.json)](COVERAGE.md) | — |
| **Stormblood** | [![SB MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/stormblood-msq.json)](COVERAGE.md) | [![SB Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/stormblood-class.json)](COVERAGE.md) | — |
| **Shadowbringers** | [![ShB MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/shadowbringers-msq.json)](COVERAGE.md) | [![ShB Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/shadowbringers-class.json)](COVERAGE.md) | [![ShB Role](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/shadowbringers-role.json)](COVERAGE.md) |
| **Endwalker** | [![EW MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/endwalker-msq.json)](COVERAGE.md) | [![EW Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/endwalker-class.json)](COVERAGE.md) | [![EW Role](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/endwalker-role.json)](COVERAGE.md) |
| **Dawntrail** | [![DT MSQ](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/dawntrail-msq.json)](COVERAGE.md) | [![DT Class](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/dawntrail-class.json)](COVERAGE.md) | [![DT Role](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/simplekantan/questforge-data/main/badges/dawntrail-role.json)](COVERAGE.md) |

---

## Structure

```
quests/
  <expansion>/
    <category>/
      <questId>-<slug>.json     Quest definition files

fragments/
  common/                       Shared step fragments usable across quests
  travel/                       Reusable travel fragments

fixtures/
  engine/                       Engine regression test fixtures (one per capability shape)
    simple-linear-acceptance.json
    ...
```

**Expansions:** `arr`, `heavensward`, `stormblood`, `shadowbringers`, `endwalker`, `dawntrail`

**Categories:** `msq`, `class`, `job`, `role`, `blue-urgent`, `blue`, `side`

| Category | Scheduler tier | Description |
|---|---|---|
| `msq` | 3 — auto chain | Main Scenario Quests |
| `class` / `job` | 1 — active job | Combat class and job quests |
| `role` | 1 — active job | Role quests (ShB+) |
| `blue-urgent` | 1 — always | Critical feature unlocks (e.g. chocobo companion) |
| `blue` | 4 — opt-in | Feature unlock quests (e.g. Gold Saucer, Hildebrand) |
| `side` | 5 — opt-in | Side story quests |

---

## Validation

Every PR that touches `quests/` or `fragments/` is validated automatically by `qf-validate` via GitHub Actions. The validator runs the full structural rule set defined in the QuestForge schema.

To run validation locally, clone `questforge-tools` as a sibling directory, then run the validator against this repo:

```bash
git clone https://github.com/simplekantan/questforge-data
git clone https://github.com/simplekantan/questforge-tools
cd questforge-data
dotnet run --project ../questforge-tools/qf-validate -- . --format text
```

CI checks out `questforge-tools` (latest `main`) automatically, so PRs are always validated against the current schema.

---

## Engine capabilities

The QuestForge engine supports 28 step types: `travel`, `talk`, `accept`, `turn-in`, `interact-object`, `pickup-item`, `combat`, `duty`, `cutscene`, `say-chat-message`, `use-emote`, `use-item`, `use-action`, `equip-gear-for-quest`, `equip-best-gear`, `change-job`, `register-gearset`, `open-coffers`, `teleport`, `aethernet`, `purchase-item`, `wait`, `minigame`, `await-user`, `branch`, `fragment`, `attune`, and `hand-over-item`.

Quest files in this repo may use any of these step types. See [CONTRIBUTING.md](CONTRIBUTING.md) for authoring guidance and predicate syntax reference.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide. Quick reference:

- One quest per file, named `<questId>-<slug>.json`
- Run `qf-validate` locally before opening a PR
- Set `enabled: false` on quests with unverified NPC IDs or positions
- Add yourself to `contributors` when authoring or making significant changes