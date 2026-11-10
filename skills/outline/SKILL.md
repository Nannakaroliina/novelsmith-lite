---
name: outline
description: Run novelsmith-lite's outline-first stages with author checkpoints — stage 1 (premise, theme, ending and act beats via the story-architect agent, starting from five or more divergent directions) and stage 3 (chapters and the scene manifest via the outliner agent, checked against the outline rules). Use when the author says "/novelsmith-lite:outline", wants to shape the premise or ending, plan acts, chapters or scenes, build or fix scenes.yaml, or check the outline before drafting.
disable-model-invocation: true
argument-hint: "[premise|scenes [repair|rebuild]|check]"
allowed-tools: Read, Glob, Grep, Edit(book/**), Agent, AskUserQuestion
---

# Outline: premise → acts → scenes

You run a stage and stop. Agents do the writing in their own context and return file paths; you check the files on disk, put the decisions in front of the author, and update `book/progress.md`. Never let an agent's claim stand in for a file you have read.

**Plugin root** is `${CLAUDE_SKILL_DIR}/../..` (resolve it to an absolute path once). Begin every agent delegation message with the line `Plugin root: <that path>`, because agents cannot work it out themselves.

**Outline check** (used below): apply `${CLAUDE_SKILL_DIR}/references/outline-check.md` by reading the files. Its result is PASS, FAIL or CANNOT CHECK, always labelled "manual check". On CANNOT CHECK, show the author the file, line and cause and stop; it is neither a pass nor a fail.

**Anchor check** (used below): around every agent launch, follow `${CLAUDE_SKILL_DIR}/references/anchor-check.md`. Take the snapshot before the launch and compare after it returns, before you check its files. The lite edition has no hook, so this is how a changed anchored decision is found.

**Choices for the author.** `AskUserQuestion` shows at most four options. When a choice has more (five or more directions, five names per role), write every option as a numbered line in your message first, then ask the author to reply with a number, a combination ("2 with the ending of 4") or their own answer. Never drop an option to fit the tool.

## 0. Where are we?

- No `book/progress.md`: tell the author to run `/novelsmith-lite:novel-start` first, and stop.
- Read `book/progress.md` and `book/bible/premise.md`.
- Stage from `$ARGUMENTS`: `premise`, `scenes`, `scenes repair`, `scenes rebuild` or `check`. There is **no manifest yet** when `book/outline/scenes.yaml` is missing or its only scene is the template's example (`ch01-s01` with empty `pov` and `goal`); otherwise there is a **real manifest**. With no argument: `premise` when the ending in `premise.md` is undecided or the beat table in `book/outline/acts.md` is empty; otherwise, if there is a real manifest, ask the author whether to check it (first option), repair its errors only, or rebuild it, and run `check`, `scenes repair` or `scenes rebuild` to match; otherwise `scenes` if the character sheets exist; otherwise tell the author the bible comes next (`/novelsmith-lite:bible`) and stop. Never relaunch the outliner over an existing manifest without the author choosing repair or rebuild, because it may contain their own edits.

## 1. Stage `premise` (story-architect)

1. **Options.** Unless the author has already decided the ending (in `premise.md`, under `## ⚓ Anchored`, or in `$ARGUMENTS`), launch the `novelsmith-lite:story-architect` agent with: "Mode: options. Book state is in book/. Today is <date>." Add anything the author said in this conversation about what they want or do not want.
2. Read the options file it wrote in `book/proposals/`. Give the author one numbered entry per direction: its logline and its ending in one or two sentences, then arc shape, who resolves and what stays open. Name the options file (its path) so they can read the full write-ups before choosing. Then ask which to develop, following **Choices for the author** above: every direction, plus "combine" and "none, regenerate". Do not recommend one. If they want a combination or a regeneration, collect their steer in their own words and run options again or pass the combination to develop. A new set goes to a new file (`premise-<date>-2.md`) and the earlier set stays where it is: name both files, so "2 with the ending of 7" still points at real directions.
3. **Develop.** Launch `novelsmith-lite:story-architect` with: "Mode: develop. Develop direction <N> from book/proposals/<file>" plus the author's adjustments verbatim. If step 1 was skipped because the author had already decided the ending, use instead: "Mode: develop. The author's ending is in book/bible/premise.md (The ending, or the anchored section); build from it." plus anything they said here.
4. **Check the files**: read `premise.md` and `acts.md`. The ending, controlling idea, counter-idea and hook must be filled and the beat table's "What happens" column complete for the declared model. If anything is missing, send the agent back once with the specific gap.
5. **Checkpoint.** Tell the author, briefly: what is now in `premise.md` and `acts.md`, the open questions the agent listed, and that decisions they are sure of belong under `## ⚓ Anchored` (agents are told never to change those, and this skill checks after every agent that none did; offer to move named decisions there for them, and do it only on their yes). Update `book/progress.md`: current stage 2, next action `/novelsmith-lite:bible`, and a decisions-log row for the direction chosen ("yes" or "no" in its Anchored? column, never the symbol). Stop.

## 2. Stage `scenes` (outliner)

1. **Preconditions** (read the files): the ending is decided; the beat table is filled; `book/bible/characters/` holds at least the protagonist and antagonist sheets. If one is missing, name it and the command that fixes it, and stop.
2. Launch the `novelsmith-lite:outliner` agent with: "Mode: <build|repair|rebuild>. Build (or repair) book/outline/scenes.yaml and chapters.md, run the outline check, and write the de-AI outline check. Today is <date>." The mode is `build` whenever there is no manifest yet (step 0), whatever the argument. With a real manifest it is the one the author chose (`scenes repair` or `scenes rebuild`); if they asked for plain `scenes`, ask them to choose between repair and rebuild first. Add any author instructions verbatim (for example "keep chapters short", "no prologue").
3. **Verify yourself**: read `book/critiques/outline/outline-check-<date>.md` and `book/critiques/outline/de-ai-outline-<date>.md`. Then go through the **Errors** table of the outline check yourself (the warnings you may take from the outliner's file). Where your result differs from the outliner's, yours is the one you report, and say that the two differed.
4. Every time you send the outliner back in this stage, begin the message with "Mode: <the mode of its first run>. Follow-up fix: do not copy the manifest again." On CANNOT CHECK, show the author the line and send the outliner back once to repair it; if the line is in a file the outliner may not write, or `book.waivers` in that file is not empty, offer to fix the line yourself on the author's yes. If the check fails on something the outliner reported needing from the author (a missing character, an ending decision), say exactly what is needed. If it fails for any other reason, send the outliner back once with the errors. Never edit `book.waivers` yourself; if the author wants to waive a check, they say so in their own words, and you add the waiver with their reason after showing them the exact lines you will add.
5. **Checkpoint.** Tell the author: the outline check result (manual check, PASS/FAIL, error and warning counts), the de-AI outline score (three or more failed checks means revise the outline before drafting), the warnings worth their attention, and where the files are. After a repair or rebuild, also name every scene the outliner changed or removed in any round of this stage, and the path of the copy its first run saved in `book/proposals/` (the author's manifest as it was before this stage), so the author can compare and take back their own edits. After a build there is no previous manifest; say nothing about one. Update `book/progress.md`: current stage 3 (or 4 once the author approves the outline), next action "review book/outline/scenes.yaml; approve or edit", and a decisions-log row ("yes" or "no" under Anchored?, never the symbol). Drafting arrives in a later version; say so if the author asks what is next. Stop.

## 3. Stage `check`

Run the outline check and report its result in a few lines: manual check, pass or fail, each error with its scene id, and the warning count. Change nothing.

## Rules

- Never decide the premise, ending, structure or names for the author. Present options; build their choice.
- Never edit anything under `## ⚓ Anchored` or `book.waivers` without the author's explicit request in this conversation.
- Launch one agent at a time in this skill; each stage ends at a checkpoint.
