# automateintelligence — Claude Code plugin marketplace

The catalog for AutomateIntelligence's Claude Code plugins. It lists three plugins, each
maintained in its own repo:

| Plugin | What it does | Repo |
|--------|--------------|------|
| **conductor** | Autonomous spec-completion loop: spec → plans → phases → machine-checked done. Works best with specs authored via SuperPowers or GitHub's spec-kit. Depends on `spec-craft`. | [automateintelligence/conductor](https://github.com/automateintelligence/conductor) |
| **spec-craft** | Make a spec's definition of done explicit and machine-checkable. Standalone, conductor-agnostic. | [automateintelligence/spec-craft](https://github.com/automateintelligence/spec-craft) |
| **bubo** | Passive code review companion: one clipped, in-character note when something is likely to break — inert until explicitly promoted. Standalone. | [automateintelligence/bubo](https://github.com/automateintelligence/bubo) |

`conductor` works best with specs authored using
[SuperPowers](https://github.com/obra/superpowers) or
[GitHub's spec-kit](https://github.com/github/spec-kit) — structured specs with explicit
requirements and phased tasks are what its loop plans and executes against; `spec-craft`
then makes "done" machine-checkable.

## Install

Add the marketplace once, then install any plugin:

```
/plugin marketplace add automateintelligence/marketplace
/plugin install spec-craft@automateintelligence     # spec-craft on its own
/plugin install conductor@automateintelligence      # conductor + spec-craft (auto-installed dependency)
/plugin install bubo@automateintelligence           # bubo on its own
```

CLI equivalents:

```bash
claude plugin marketplace add automateintelligence/marketplace
claude plugin install spec-craft@automateintelligence
claude plugin install conductor@automateintelligence
claude plugin install bubo@automateintelligence
```

`conductor` declares `spec-craft` as a dependency, so installing conductor pulls spec-craft
automatically. Installing `spec-craft` alone pulls only spec-craft. `bubo` is fully
standalone — it is never installed alongside the others unless you ask for it.

## How it works

This repo ships only `.claude-plugin/marketplace.json` — a catalog whose entries point at the
plugin repos by git URL. The plugin code lives in the plugin repos, not here. Marketplace
installs track each repo's default branch; pin a `ref`/`sha` in the catalog entry to freeze a
version.
