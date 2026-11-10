# Changelog

## 0.1.0 — 2026-11-10

First release of the lite edition, made from [Novelsmith](https://github.com/Nannakaroliina/novelsmith) 0.3.18 for the Claude plugin directory.

- Same four planning agents, stages, templates, craft and genre references, and de-AI checks. Commands are namespaced `/novelsmith-lite:…`.
- No scripts or hooks. The outline is checked by reading the files against `skills/outline/references/outline-check.md` (the full edition's validator rules), labelled "manual check". After the outliner returns, the outline skill goes through the errors again itself.
- New `skills/outline/references/anchor-check.md`: the `outline` and `bible` skills compare anchored text, waivers and the premise's genre and structure settings before and after each agent, and offer to restore any change.
- The outliner has no shell access. Doctor checks files, settings and agents only.
- Changes from the full edition beyond packaging: a bare `/novelsmith-lite:outline` asks whether to check, repair or rebuild an existing manifest instead of relaunching the outliner over it, with new `scenes repair` (change only what an error needs) and `scenes rebuild` modes; the story-architect never changes the premise's genre, variant or structural model, and novel-start replaces their example values; de-ai uses the `avoid-ai-writing` vocabulary list only when that skill is installed, and a bundled short list otherwise, and writes outline critiques to `book/critiques/outline/`; `premise.md` has an Author line.
- Left out: the canon guard hook (it blocked agents from changing anchored decisions and from writing outside their folders), `validate-outline.py`, the self-check, the installer and the Cowork zip builder.
