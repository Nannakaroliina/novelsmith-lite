# Novelsmith Lite

A Claude plugin for writing novels with a multi-agent, outline-first workflow. The book's state lives in a `book/` tree in your project; agents read and write files there, propose changes, and stop at every stage boundary so you decide.

This is the lite edition of [Novelsmith](https://github.com/Nannakaroliina/novelsmith). It is made only of skills, agents and templates, with no scripts or hooks, so it installs from the Claude plugin directory. It plans a book the same way as the full edition: the same agents, stages, craft references, genre contracts and de-AI checks. What it leaves out is the enforcement:

| | Full edition | Lite edition |
|---|---|---|
| Outline check | `validate-outline.py`: exact and repeatable | The same rules applied by reading the files, labelled "manual check" |
| Anchored decisions (⚓) | A hook blocks any agent that tries to change them | Agents are told not to; the skills compare the anchored text before and after each agent and show you any change to restore |
| Agents' write scopes | A hook keeps each agent in its own folders | Each agent's instructions only |
| Requirements | Python 3 | None (de-ai uses the separate `avoid-ai-writing` skill's vocabulary list if you have it installed, and a short bundled list otherwise) |

If you want the guarantees, use the full edition.

**Status: Phase 2 (outline-first core), MIT licence.** Scaffolding, craft knowledge, the de-AI taxonomy, mystery and Nordic/Finnish crime references, and the four planning agents. Drafting, critique and revision arrive in later versions.

## Install

From the Claude plugin directory, or add this repository as a marketplace in Claude Code:

```text
/plugin marketplace add Nannakaroliina/novelsmith-lite
/plugin install novelsmith-lite@novelsmith-lite
```

Then restart Claude Code (or run `/reload-plugins`). Open the folder that holds (or will hold) your `book/`, your writing project, and run `/novelsmith-lite:doctor`, then `/novelsmith-lite:novel-start`.

In Cowork, plugin settings are not prompted for: `/novelsmith-lite:novel-start` asks for your name, genre and length instead. Chat without Cowork ignores agents, so the staged workflow needs Cowork or Claude Code.

## The workflow

| Stage | Invoke | Agent(s) | Writes | Checkpoint |
|---|---|---|---|---|
| 0 Start | `/novelsmith-lite:novel-start [title]` | — | `book/` tree from `templates/` | You fill in what you know |
| 1 Premise | `/novelsmith-lite:outline premise` | `story-architect` | 5+ divergent directions in `proposals/`, then `bible/premise.md`, `outline/acts.md` | You pick a direction and anchor decisions |
| 2 Bible | `/novelsmith-lite:bible [all\|characters\|world\|<name>]` | `character-designer` ∥ `worldbuilder` | Name candidates in `proposals/`, then `bible/characters/*.md`, `bible/world/*.md`, `bible/timeline.md` | You pick names and check `[verify]` facts |
| 3 Outline | `/novelsmith-lite:outline scenes` | `outliner` | `outline/scenes.yaml`, `outline/chapters.md`, clue plants, `critiques/outline/outline-check-<date>.md`, `critiques/outline/de-ai-outline-<date>.md` | Outline check passes; you approve |
| — | `/novelsmith-lite:outline scenes repair` or `scenes rebuild` | `outliner` | Repair changes only what an error needs; rebuild plans the manifest again | Same as stage 3 |
| — | `/novelsmith-lite:outline check` | — | nothing | Outline check report only |

Once `scenes.yaml` holds scenes, a bare `/novelsmith-lite:outline` asks whether to check, repair or rebuild it, so your own edits are never planned over without your say.

## Components

| Component | Invoke | Purpose |
|---|---|---|
| `skills/novel-start` | `/novelsmith-lite:novel-start [title]` | Interviews you and scaffolds `book/` from `templates/` |
| `skills/outline` | `/novelsmith-lite:outline [premise\|scenes [repair\|rebuild]\|check]` | Stages 1 and 3 with checkpoints; `references/outline-check.md`, `references/anchor-check.md` |
| `skills/bible` | `/novelsmith-lite:bible [scope]` | Stage 2 with checkpoints; the route for changing canon |
| `skills/doctor` | `/novelsmith-lite:doctor` | Checks plugin files, settings and agents in this environment |
| `skills/genre` | `/novelsmith-lite:genre [genre]` or auto | Loads one genre contract; `references/mystery.md`, `references/mystery-nordic-finnish.md` |
| `skills/de-ai` | `/novelsmith-lite:de-ai <path> [--detect\|--rewrite]` | Fiction-specific AI-tell audit; four reference tiers |
| `skills/craft-structure`, `craft-character`, `craft-prose` | auto | Beat map and scene test; arc engine and voice; line-level craft |
| `agents/story-architect` | via `outline` | Divergent premises and endings, then premise + beat table |
| `agents/character-designer` | via `bible` | Cast roles, name candidates, character sheets with voice and knowledge ledger |
| `agents/worldbuilder` | via `bible` | Places and institutions as pressure; backstory and hidden crime timeline |
| `agents/outliner` | via `outline` | Scene manifest, chapters, clue plants; check-and-fix loop; de-AI outline check |
| `templates/` | — | Premise, style sheet, character, world, timeline, acts, scene manifest, clue ledger, summaries, critique note, progress |

## The book tree

```text
book/
├── bible/        premise.md · style.md · timeline.md · clues.md · characters/ · world/
├── outline/      acts.md · chapters.md · scenes.yaml
├── manuscript/   chNN/sNN.md
├── summaries/    chNN.md · running.md
├── critiques/    outline/ · chNN/<agent>-<date>.md
├── proposals/    divergent options and proposed changes awaiting you
└── progress.md
```

## The outline check

The outliner and the outline skill apply `skills/outline/references/outline-check.md`, the same rules as the full edition's validator, by reading the files. The result is PASS, FAIL or CANNOT CHECK, always labelled "manual check". After the outliner returns, the skill goes through the errors again itself rather than taking the agent's word for them.

- **Cannot check:** scenes.yaml missing or not valid YAML, a repeated key, or `book` / `book.waivers` not a mapping. The report names the line.
- **Errors (block drafting):** `base_genre` or `structure` differing from premise.md; missing goal/conflict/turn/outcome/value shift; outcome not `yes-but`/`no-and` (sequels excepted); malformed or duplicate ids, chapters out of order; a POV or character with no sheet; a model-default name (Elias, Mara, Elara…) you have not anchored, or a name with invisible characters or mixed alphabets; for the arc model, a missing or out-of-order beat or a climax before 80%; no subplot; no `compromise`, `non_protagonist_resolution` or open thread; for mysteries, unknown clue ids and any clue paid off before it is planted.
- **Warnings:** beat placement, a long tail after the climax, uniform chapters, too few `no-and` setbacks, repeated outcomes, consecutive sequels, no non-linear move, length drift, mystery placement and the Nordic/Finnish subplot tags.
- **Waivers** live in `book.waivers` in `scenes.yaml` and belong to you: a waived error is reported but does not fail.

A careful reading is not a script. On a long manifest it can miss a beat percentage or a clue order that a script would catch. Run `/novelsmith-lite:outline check` again after you edit `scenes.yaml` by hand.

## Anchored decisions

Put decisions you are sure of under `## ⚓ Anchored` in `premise.md`, or mark any line with ⚓. Agents are told never to change, move or add ⚓ lines, waivers or the genre and structure settings in `premise.md`.

Nothing in the lite edition can stop a write. Instead, the `outline` and `bible` skills take a snapshot of every anchored line and section before launching an agent and compare it when the agent returns. If anything changed, you see the before and after and choose whether to restore it. This catches changes to anchored text only, and only in runs started from those skills. It does not notice an agent writing other files outside its folders. When you edit the book yourself, nothing is checked.

## Getting help

Run `/novelsmith-lite:doctor` first and include its table. Report problems at <https://github.com/Nannakaroliina/novelsmith-lite/issues>.

## Privacy and terms

Novelsmith Lite collects no data: no server, no account, no telemetry. See the [Privacy Policy](PRIVACY.md) and [Terms of Use](TERMS.md).

## License

MIT © 2026 Nanna Setämaa
