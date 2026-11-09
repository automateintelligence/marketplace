# automateintelligence — Claude Code and OpenAI Codex plugin marketplace

The catalog for AutomateIntelligence's plugins, installable from Claude Code and OpenAI Codex (Marshal: Claude Code only for now). It lists four plugins, each
maintained in its own repo:

| Plugin | What it does | Repo |
|--------|--------------|------|
| **conductor** | Autonomous spec-completion loop: spec → plans → phases → machine-checked done. Works best with specs authored via SuperPowers or GitHub's spec-kit. Depends on `spec-craft`. | [automateintelligence/conductor](https://github.com/automateintelligence/conductor) |
| **spec-craft** | Make a spec's definition of done explicit and machine-checkable. Standalone, conductor-agnostic. | [automateintelligence/spec-craft](https://github.com/automateintelligence/spec-craft) |
| **bubo** | Passive code review companion: one clipped, in-character note when something is likely to break — inert until explicitly promoted. Standalone. | [automateintelligence/bubo](https://github.com/automateintelligence/bubo) |
| **marshal** | A manager session for your other Claude Code sessions: shared worker rules in every session, the grilling and handoff skills, and `/marshal:init` to create your private Marshal workspace. Standalone. Claude Code only for now. | [automateintelligence/marshal](https://github.com/automateintelligence/marshal) |

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
/plugin install marshal@automateintelligence         # marshal on its own (Claude Code only; steps in the Marshal section below)
```

CLI equivalents: `claude plugin marketplace add automateintelligence/marketplace`, then
`claude plugin install <plugin>@automateintelligence`.

`conductor` declares `spec-craft` as a dependency, so on Claude Code installing conductor
pulls spec-craft automatically. `bubo` and `marshal` are fully standalone.

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

**marshal on Codex:** OpenAI Codex: not supported yet. See [ROADMAP.md](https://github.com/automateintelligence/marshal/blob/main/ROADMAP.md).

## Marshal

Marshal is one Claude Code session that manages your other Claude Code sessions. You keep doing the work in
as many sessions as you like (different projects, different repos, tmux windows on a server or local). 

Marshal:
- watches them (tmux panes, transcripts, repos, PRs),
- relays your decisions to the right session via messaging,
- makes the easy calls for you under rules you set, and says so,
- asks you the hard questions as short y/n or labeled a/b/c questions in plain language with context 
  and consequences of the decisions,
- keeps what it knows in `state/*.md`, committed to git, so a restart or a new machine loses nothing,
- never writes product code itself.

Marshal has two parts:

1. **The plugin** (installed from the marketplace into Claude Code). It goes to EVERY session you run, in any repo: a SessionStart hook hands each session the worker rules in `docs/worker-rules.md` (how to ask you questions, review loops, reporting, naming, no flaky tests), so you do not edit `~/.claude/CLAUDE.md`. It also carries the skills `/marshal:handoff`, `/marshal:grilling`, `/marshal:grill-with-docs` and `/marshal:domain-modeling`, and the command `/marshal:init` that creates your workspace.
2. **Your Marshal workspace.** Your own repo, PRIVATE, holding `CLAUDE.md`, `state/` and `docs/` for the Marshal session itself. Create it with `/marshal:init`, or use the public Marshal repo (github.com/automateintelligence/marshal) as a template.

### Install on Claude Code

1. Add the marketplace (once):

   ```
   /plugin marketplace add automateintelligence/marketplace
   ```

2. Install the plugin. When Claude Code asks for a scope, pick **user**, so every session you run gets it:

   ```
   /plugin install marshal@automateintelligence
   ```

3. Restart Claude Code, or run `/reload-plugins`.

4. Create your workspace: a new folder with Marshal's rules and your `state/`. Claude copies the templates, runs `git init`, installs a push guard and walks you through the `{{placeholders}}`:

   ```
   /marshal:init ~/marshal-workspace
   ```

5. Put the workspace in your own PRIVATE repo (replace `<you>` with your GitHub name):

   ```
   cd ~/marshal-workspace && git init && git add -A && git commit -m "Marshal workspace" && gh repo create <you>/my-marshal --private --source . --remote github --push
   ```

   Never fork the public Marshal repo for your workspace (a fork of a public repo is public), and never push your filled-in `state/` to the public Marshal repo.

6. Start Marshal in that folder with Remote Control (`<where>` is `local`, `wsl`, `srv` or `cloud`), then paste the two loop prompts from [The two loops](https://github.com/automateintelligence/marshal#the-two-loops):

   ```
   cd ~/marshal-workspace && claude --remote-control '[MSL] OPS: Marshal -<where>'
   ```

Template route, instead of steps 4 and 5 (the public Marshal repo is the template; the plugin from steps 1 to 3 still gives every session the worker rules):

```
gh repo create <you>/my-marshal --private --template automateintelligence/marshal --clone
cd my-marshal && sh scripts/install-git-hooks.sh
```

Then follow [Without the plugin](https://github.com/automateintelligence/marshal#without-the-plugin-use-this-repo-as-a-template) from step 2.

### OpenAI Codex

OpenAI Codex: not supported yet. See [ROADMAP.md](https://github.com/automateintelligence/marshal/blob/main/ROADMAP.md).

### Keeping your state private

Your filled-in `state/` holds notes about your work. It must live only in your own PRIVATE repo.

- Make your own private repo (install step 5, or the template route). Never fork the public Marshal repo for your workspace: a fork of a public repo is public. Never push your filled-in `state/` to the public Marshal repo.
- `/marshal:init` makes a fresh repo with no history and no remote, and installs a git `pre-push` guard (`scripts/git-hooks/pre-push`; template users run `sh scripts/install-git-hooks.sh`). The guard refuses any push to github.com/automateintelligence/marshal (https or ssh, with or without `.git`). It also refuses a push to any repo GitHub reports as public when `state/` differs from the shipped templates. That check needs the `gh` CLI; without `gh` the guard blocks only the public Marshal repo and prints a warning.
- In the public Marshal repo, a GitHub Actions check (`.github/workflows/guard-state.yml`) fails any pull request that changes `state/` unless it carries the `template-change` label.

## How it works

This repo ships only `.claude-plugin/marketplace.json` — a catalog whose entries point at the
plugin repos by git URL. Claude Code and Codex both read it. The plugin code lives in the plugin repos, not here. Marketplace
installs track each repo's default branch; pin a `ref`/`sha` in the catalog entry to freeze a
version.
