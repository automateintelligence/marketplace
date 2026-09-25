# automateintelligence — Claude Code and OpenAI Codex plugin marketplace

The catalog for AutomateIntelligence's plugins, installable from Claude Code and OpenAI Codex. It lists three plugins, each
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

This is the plugin install for every plugin in the catalog. Each plugin's own README (linked
in the table above) repeats its install and also covers running it **without** the plugin,
from a clone.

### Claude Code

Add the marketplace once, then install any plugin:

```
/plugin marketplace add automateintelligence/marketplace
/plugin install conductor@automateintelligence      # conductor + spec-craft (auto-installed dependency)
/plugin install spec-craft@automateintelligence     # spec-craft on its own
/plugin install bubo@automateintelligence           # bubo on its own
```

CLI equivalents: `claude plugin marketplace add automateintelligence/marketplace`, then
`claude plugin install <plugin>@automateintelligence`.

`conductor` declares `spec-craft` as a dependency, so on Claude Code installing conductor
pulls spec-craft automatically. `bubo` is fully standalone.

### OpenAI Codex

Codex (0.155.0+) reads this same catalog. Codex does not resolve plugin dependencies, so
install spec-craft alongside conductor explicitly:

```bash
codex plugin marketplace add automateintelligence/marketplace
codex plugin add conductor@automateintelligence
codex plugin add spec-craft@automateintelligence
codex plugin add bubo@automateintelligence           # standalone
```

On Codex, plugin skills are invoked by their qualified names, e.g. `$conductor:start`.

**bubo on Codex:** after `codex plugin add bubo@automateintelligence`, approve its hooks when
Codex asks and start plain `codex` — no launcher. The hooks run Bubo's passive review; type Bubo
commands in plain words (`bubo status`, `bubo review`). Codex plugins cannot add slash commands,
so `/bubo` needs bubo's clone setup — see
[bubo's install guide](https://github.com/automateintelligence/bubo#installation).

## How it works

This repo ships only `.claude-plugin/marketplace.json` — a catalog whose entries point at the
plugin repos by git URL. Claude Code and Codex both read it. The plugin code lives in the plugin repos, not here. Marketplace
installs track each repo's default branch; pin a `ref`/`sha` in the catalog entry to freeze a
version.
