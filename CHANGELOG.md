# Changelog

## 0.1.0 — 2026-11-10

First release of the lite edition, made from [Novelsmith](https://github.com/Nannakaroliina/novelsmith) 0.3.18 for the Claude plugin directory.

- Same four planning agents, stages, templates, craft and genre references, and de-AI checks. Commands are namespaced `/novelsmith-lite:…`.
- No scripts or hooks. The outline is checked by reading the files against `skills/outline/references/outline-check.md` (the full edition's validator rules), labelled "manual check". After the outliner returns, the outline skill goes through the errors again itself.
- New `skills/outline/references/anchor-check.md`: the `outline` and `bible` skills compare anchored text, waivers and the premise's genre and structure settings before and after each agent, and offer to restore any change.
- The outliner has no shell access. Doctor checks files, settings and agents only.
- Left out: the canon guard hook (it blocked agents from changing anchored decisions and from writing outside their folders), `validate-outline.py`, the self-check, the installer and the Cowork zip builder.
