# questforge-data

Quest definitions and fragment library for [QuestForge](https://github.com/simplekantan/questforge).

[![Validate quest data](https://github.com/simplekantan/questforge-data/actions/workflows/validate.yml/badge.svg)](https://github.com/simplekantan/questforge-data/actions/workflows/validate.yml)
[![License: CC BY 4.0](https://img.shields.io/badge/license-CC--BY--4.0-lightgrey.svg)](LICENSE)

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

To run validation locally (requires the `questforge-tools` submodule):

```bash
git clone --recurse-submodules https://github.com/simplekantan/questforge-data
cd questforge-data
dotnet run --project questforge-tools/qf-validate -- . --format text
```

If you already cloned without `--recurse-submodules`:

```bash
git submodule update --init
dotnet run --project questforge-tools/qf-validate -- . --format text
```

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide. Quick reference:

- One quest per file, named `<questId>-<slug>.json`
- Run `qf-validate` locally before opening a PR
- Set `enabled: false` on quests with unverified NPC IDs or positions
- Add yourself to `contributors` when authoring or making significant changes