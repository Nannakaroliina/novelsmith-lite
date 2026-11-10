# Anchor check (lite edition)

The lite edition has no hook, so nothing stops an agent from changing the author's anchored decisions. This check finds such a change after the agent returns, so the author can restore it. Run it around every agent launch in the `outline` and `bible` skills.

## What counts as anchored

- Every line in `book/` that contains ⚓, `&#9875;` or `:anchor:`.
- Every section whose heading contains one of those marks: the heading and everything under it up to the next heading of the same or a higher level (such as `## ⚓ Anchored` in `premise.md`).
- `book.waivers` in `book/outline/scenes.yaml`.
- The "Base genre", "Variant" and "Structural model" values in `book/bible/premise.md`.

## Before launching an agent

1. Grep `book/` for `⚓|&#9875;|:anchor:` with line numbers.
2. Read each anchored section in full, and the waivers and premise settings above.
3. Keep this snapshot, with each item's file and its place (the heading it sits under, or a scene's `- id:` line in `scenes.yaml`). Do not write it to a file.

## After the agent returns

1. Take the snapshot again the same way.
2. Compare item by item. A change is any anchored line or section whose text differs, is gone, has moved to a different heading or scene, or is new (an agent added a ⚓ mark), and any change to the waivers or the three premise settings.
3. Nothing changed: say nothing about it.
4. Something changed: before the checkpoint, show the author each change as before and after, with its file. Ask whether to restore it. Restore only on their yes, with Edit, putting back the exact earlier text. Never keep or undo a change without asking.

## Limits

This check finds changes to anchored text after they are written; it does not prevent them, and it does not notice an agent writing other files outside its folders. Each agent's own instructions are what keep it in its folders.
