# automateintelligence — Claude Code plugin marketplace

The catalog for AutomateIntelligence's Claude Code plugins. It lists two plugins, each
maintained in its own repo:

| Plugin | What it does | Repo |
|--------|--------------|------|
| **conductor** | Autonomous spec-completion loop: spec → plans → phases → machine-checked done. Depends on `spec-craft`. | [automateintelligence/conductor](https://github.com/automateintelligence/conductor) |
| **spec-craft** | Make a spec's definition of done explicit and machine-checkable. Standalone, conductor-agnostic. | [automateintelligence/spec-craft](https://github.com/automateintelligence/spec-craft) |

## Install

Add the marketplace once, then install either plugin:

```
/plugin marketplace add automateintelligence/marketplace
/plugin install spec-craft@automateintelligence     # spec-craft on its own
/plugin install conductor@automateintelligence      # conductor + spec-craft (auto-installed dependency)
```

CLI equivalents:

```bash
claude plugin marketplace add automateintelligence/marketplace
claude plugin install spec-craft@automateintelligence
claude plugin install conductor@automateintelligence
```

`conductor` declares `spec-craft` as a dependency, so installing conductor pulls spec-craft
automatically. Installing `spec-craft` alone pulls only spec-craft.

## How it works

This repo ships only `.claude-plugin/marketplace.json` — a catalog whose entries point at the
plugin repos by git URL. The plugin code lives in the plugin repos, not here. Marketplace
installs track each repo's default branch; pin a `ref`/`sha` in the catalog entry to freeze a
version.
