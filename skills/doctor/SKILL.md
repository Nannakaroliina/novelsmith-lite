---
name: doctor
description: Check that novelsmith-lite works in this environment (especially Cowork) — plugin files found, settings, and the four planning agents — and explain what the lite edition does without scripts or hooks. Use when the author says "/novelsmith-lite:doctor", after installing or updating the plugin, or when an agent or skill seems not to find its files.
disable-model-invocation: true
allowed-tools: Read, Glob
---

# Novelsmith-lite doctor

Run every check, then report one table. Change nothing.

**Plugin root** is `${CLAUDE_SKILL_DIR}/../..`. Resolve it to an absolute path.

1. **Plugin files.** Confirm these exist under the plugin root: `templates/scenes.yaml`, `templates/premise.md`, `skills/outline/references/outline-check.md`, `skills/outline/references/anchor-check.md`. If any is missing, the skills cannot hand agents the plugin root; report the path you resolved.
2. **Settings.** Author `${user_config.author_name}`, genre `${user_config.default_genre}`, length `${user_config.target_words}`. A value that is empty or still reads as a `${…}` placeholder is unset; that is expected in Cowork, which does not prompt for plugin settings, and `/novelsmith-lite:novel-start` asks instead.
3. **Agents.** Check that `novelsmith-lite:story-architect`, `novelsmith-lite:character-designer`, `novelsmith-lite:worldbuilder` and `novelsmith-lite:outliner` are available to the Agent tool.
4. **Book.** If a `book/` folder exists, check that `book/progress.md` is in it; otherwise mark it "none yet: run /novelsmith-lite:novel-start".

## Report

| Check | Result | What it means for writing |
|---|---|---|
| Plugin files | found at `<path>` / missing: … | |
| Settings | set / unset (asked at novel-start) | |
| Agents | 4 of 4 / missing: … | |
| Book | found / none yet | |
| Outline check | by reading the files (lite edition) | labelled "manual check"; slower and less exact than a script |
| Anchored decisions | checked by the skills after each agent (lite edition) | nothing blocks a write; a changed ⚓ line is found afterwards and you decide whether to restore it |

End with one line: "Ready" if checks 1 and 3 passed; otherwise the single most important problem and its consequence. Do not offer fixes that need changing the plugin; say the author can report them to the plugin's author (see the README).
