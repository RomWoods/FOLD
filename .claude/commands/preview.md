---
description: Apply a change to a local preview copy of the site and open it in the browser
---

Make the requested change to a **preview copy**, not to the live file.

1. If `preview.html` doesn't exist, create it: `cp index.html preview.html`.
   If it already exists and `index.html` is newer, refresh it the same way first.
2. Make the requested edit in `preview.html` only. Never touch `index.html` at this stage.
   Remember the 11.5 MB rule in CLAUDE.md — no whole-file reads, use a Python script.
3. Open it: `open preview.html`.
4. Tell Rom in one or two lines what changed and where to look for it
   (which fold section, which card), then ask whether to publish it.

Do not commit, do not push, do not modify `index.html` until Rom says yes.
When he approves, run `/deploy`.

$ARGUMENTS
