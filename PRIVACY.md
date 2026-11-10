# Privacy Policy

Last updated: 10 November 2026

Novelsmith Lite is a free, open-source plugin for Claude, made by Nanna Setämaa ("I", "me"). This policy explains what happens to your data when you use it.

**Short version: Novelsmith Lite collects nothing. It has no server, no account, no analytics and no telemetry. It is a set of instructions, agents and templates that run inside Claude Code or Cowork. Your book is read and written by Claude, so it goes to Anthropic like any text you ask Claude to work on. The only files it writes are in the `book/` folder of the project you open.**

## What Novelsmith Lite does with your data

Novelsmith Lite helps you plan a novel in a folder you choose. To do that, Claude:

- asks you for your name as the author, a working title, genre, length and, if you want, samples of your own prose;
- writes your answers and the book's planning files (premise, style sheet, characters, world, timeline, outline, critiques, proposals, progress) as Markdown and YAML files in `book/`;
- reads those files again in later stages.

It runs no programs and makes no network requests of its own. I never receive any of this. Novelsmith Lite contains no code that sends data to me or to any server I run.

## Where your data does go

Novelsmith Lite works through Claude, and that has its own terms. Everything Claude reads or writes for the book, including your prose samples and your name, is sent to Anthropic (or to the cloud provider you have configured for Claude Code) as part of your normal session. That is governed by your agreement with Anthropic and [Anthropic's Privacy Policy](https://www.anthropic.com/legal/privacy), not by Novelsmith Lite.

## Personal data

Novelsmith Lite does not ask for personal data beyond your name as the author, which it writes into `book/progress.md` and `book/bible/premise.md`. You can leave it out or use a pen name.

Your book may draw on real people or places. Anything you put in it is read by Claude and sent to Anthropic as described above, so think about that before including other people's personal details.

## Deleting your data

- Delete the `book/` folder, or any file in it, to remove what Novelsmith Lite wrote.
- Uninstalling the plugin removes the plugin itself; it does not touch `book/`.

## Children

Novelsmith Lite is a writing tool for adults and is not directed at children.

## Changes

If this policy changes, the new version will be published in this file in the repository, with a new date at the top. The repository's history keeps every earlier version.

## Contact

Questions about this policy: open an issue at <https://github.com/Nannakaroliina/novelsmith-lite/issues>.
