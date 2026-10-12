# Claude Supercharge

A collection of Claude Code mods, skills and plugins, vendored here so a cloud session can load them without fetching anything.

Mods are built on function hooks (`hooks/register.tsx`), an early-access Claude Code API. Every mod below passes `claude plugin validate` on Claude Code 2.1.296.

## What's in `mods/`

| Mod | Type | Try it | What it does | Upstream (commit) | License |
| --- | --- | --- | --- | --- | --- |
| [skins](mods/skins) | mod + skill | `/skin`, `/skin dracula`, `/skin off` | Restyles tool rows with icons and timings, draws diffs, tables and Mermaid charts as cards, adds a usage band, and ships fifteen themes. | [hellosverre/claude-skins](https://github.com/hellosverre/claude-skins) (`079b2d1`) | MIT |
| [filetree](mods/filetree) | mod | `/filetree` | An IDE-style file tree pane with git status and line counts. Files Claude reads or edits shimmer. | [data-goblin/claude-code-filetree](https://github.com/data-goblin/claude-code-filetree) (`b0e4098`) | MIT |
| [replay-theater](mods/replay-theater) | mod | `/replay` | Steps through the file edits from the last turn, one diff at a time. | [anthropics/claude-code-playground](https://github.com/anthropics/claude-code-playground/tree/main/claude-code/mods/replay-theater) (`569c528`) | Apache-2.0 |
| [savvy-progress](mods/savvy-progress) | mod | `/agents-info` | A progress bar above the prompt and a live panel listing running subagents. | [JohnnyVizz/claude-kit](https://github.com/JohnnyVizz/claude-kit/tree/main/plugins/savvy-progress) (`272a877`) | MIT |
| [savvy-flow](mods/savvy-flow) | skill + agents | `/savvy-flow:savvy-flow <task>` | The session model plans the task, hands parts to tiered worker subagents, and reviews their work. It drives the savvy-progress bar. | [JohnnyVizz/claude-kit](https://github.com/JohnnyVizz/claude-kit/tree/main/plugins/savvy-flow) (`272a877`) | MIT |
| [reflect-mod](mods/reflect-mod) | mod | `/reflect-queue`, `/reflect-pause` | Checks each short prompt for a correction. When it finds one, it offers to save it as a `CLAUDE.md` rule from a band above the prompt. | [BayramAnnakov/claude-reflect](https://github.com/BayramAnnakov/claude-reflect/tree/main/mod) (`b6c4232`) | MIT |

Each folder keeps its upstream `README.md` and `LICENSE`. Only the files needed to run each mod were copied: the manifest, `hooks/`, `types/`, `skills/` and `agents/`. Tests, media and CI were left out.

### Things to know

- **filetree** draws only on the `terminal` and `desktop` surfaces. The Claude mobile app shows nothing for it.
- **skins**: the `/skin` settings pane is not available on mobile. `/skin <name>` still switches themes there. On startup, skins fetches its own upstream `plugin.json` to check for updates.
- **reflect-mod** sends each short prompt (500 characters or less, no slash commands or acknowledgements) to `haiku` through `$.model.complete`, so it costs a small number of tokens per prompt. It writes to `CLAUDE.md` only when you press Save in its band. `/reflect-pause` turns it off.
- **savvy-flow** agents request the `opus` model, and `savvy-fable` requests `fable`.
- The upstream `claude-reflect` Python plugin was left out on purpose. Its name is reserved, so it fails validation, and upstream says to run the plugin or the mod, never both.

## Loading them

### In a cloud session (claude.ai/code)

Set this environment variable in the cloud environment's settings (open the environment menu in the session's title bar, then choose **Edit**). New sessions pick it up:

```
CLAUDE_CODE_PLUGIN_DIRS=/home/user/Claude_supercharge/mods/skins:/home/user/Claude_supercharge/mods/filetree:/home/user/Claude_supercharge/mods/replay-theater:/home/user/Claude_supercharge/mods/savvy-progress:/home/user/Claude_supercharge/mods/savvy-flow:/home/user/Claude_supercharge/mods/reflect-mod
```

Drop any path to leave that mod out.

### Locally

Load one mod for a single session:

```bash
claude --plugin-dir ./mods/skins
```

Or install from this repository as a marketplace:

```
/plugin marketplace add DirenDenizYildirim/Claude_supercharge
/plugin install skins@supercharge
```

## Updating a mod

Clone the upstream repo, copy the same folders over the matching `mods/<name>/` folder, update the commit hash in the table above, and run `claude plugin validate mods/<name>`.
