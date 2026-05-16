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
      <id>-<slug>.json        Quest definition files
fragments/
  <namespace>/
    <slug>.json               Reusable step fragments
```

Categories: `msq`, `class`, `job`, `side`, `tribe`

Expansions: `arr`, `heavensward`, `stormblood`, `shadowbringers`, `endwalker`, `dawntrail`

---

## Validation

Every PR that touches `quests/` or `fragments/` is validated by `qf-validate` via GitHub Actions. The validator runs the full structural rule set defined in the QuestForge schema.

To run validation locally:

```bash
git submodule update --init
dotnet run --project questforge-tools/qf-validate -- . --format text
```

---

## Setup (first-time clone)

```bash
git clone --recurse-submodules https://github.com/simplekantan/questforge-data
```

If you already cloned without `--recurse-submodules`:

```bash
git submodule update --init
```

---

## Submodule setup (maintainers only)

The `questforge-tools` submodule must be added once the tools repo has a GitHub remote:

```bash
git submodule add https://github.com/simplekantan/questforge-tools questforge-tools
git commit -m "Add questforge-tools submodule"
```

---

## Contributing

- One quest per file, named `<questId>-<slug>.json`
- Run `qf-validate` locally before opening a PR
- Set `enabled: false` on quests with unverified NPC IDs or positions
- Add yourself to `contributors` when authoring or making significant changes