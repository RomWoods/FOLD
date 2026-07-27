---
description: Publish the approved preview to fold.gallery (only after Rom has said yes)
---

Only run this after Rom has looked at `preview.html` and explicitly approved it.
If he hasn't seen the change yet, run `/preview` instead and ask.

1. If `preview.html` exists, promote it: `cp preview.html index.html`, then delete
   `preview.html`. If it doesn't exist, the change is already in `index.html`.
2. Sanity-check `index.html` before committing — it must still contain all four
   `<article class="fold">` sections (`head`, `body`, `legs`, `feet`) and be roughly
   11-12 MB. If it looks truncated or the size dropped sharply, stop and warn Rom.
3. `git add -A`, then commit with a short plain-English summary of what actually changed
   ("add Cardigan Knight to the cast", "fix tee prices"). No boilerplate.
4. `git push origin main`.
5. Tell Rom it's live in a minute or two, and that he should hard-refresh
   (Cmd+Shift+R). Favicon changes cache hard and can take longer.

If the push fails on authentication, don't retry blindly — report the exact error and
suggest `gh auth login`, or committing through GitHub Desktop this once.

$ARGUMENTS
