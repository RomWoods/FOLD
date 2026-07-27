# FOLD — fold.gallery

Single-page site for **FOLD**, an ongoing Exquisite Corpse art project. Two artists,
one sheet of paper, no looking. Live at <https://fold.gallery>.

## Repo facts

- Remote: `https://github.com/RomWoods/FOLD.git`, branch `main`
- Hosting: **GitHub Pages** from `main` (root). `CNAME` holds the custom domain `fold.gallery`
- No build step, no dependencies, no framework. `index.html` is the whole site
- Rom works in the **Code tab of the Claude desktop app**, not the terminal. Git commands
  are pre-approved in `.claude/settings.json` so you can commit and push directly —
  but only after he's seen and approved the change (see Workflow below). Force-push and
  hard-reset are blocked on purpose

```
FOLD-live/
├── index.html   ← the entire site (~11.5 MB, all HTML + CSS + JS + images inline)
├── CNAME        ← fold.gallery
├── .gitattributes
└── CLAUDE.md    ← this file
```

## The 11.5 MB warning — read this before touching index.html

All 184 images (78 PNG, 106 JPEG) are **base64 data URIs embedded inline**. The markup,
CSS and JS together are only ~45 KB; the other 11.5 MB is image payload.

Consequences:

- **Never `Read` or `cat` the whole file.** Use targeted `Grep`/`Edit`, or a Python script
  that reads it, edits it and writes it back.
- To inspect structure, strip the blobs first:
  ```python
  import re
  s = open('index.html', encoding='utf-8').read()
  print(re.sub(r'data:[a-zA-Z0-9/+.-]+;base64,[A-Za-z0-9+/=\s]+', 'data:[BASE64]', s))
  ```
- GitHub Desktop refuses to render a diff for this file ("too large"). That's cosmetic —
  commits and pushes work fine.
- Adding a new full-size artwork adds roughly its own file size × 1.33 to the repo.
  Compress before embedding (target ≤ 300 KB per image; the current largest is 700 KB).

## Page structure

The site is a four-part figure mirroring the exquisite-corpse fold: **head → body → legs →
feet**. Each is an `<article class="fold" id="…">` inside `<main class="folds">`, collapsed
by default and opened by clicking its `.fold-head`.

| `#id`   | Nav label     | Contents |
|---------|---------------|----------|
| `head`  | The Process   | Explainer + interactive fold demo (`#lf-card` / `#lf-stack`) |
| `body`  | The Cast      | 56 character cards (`.gchar`), each opening the lightbox |
| `legs`  | The Tees      | Screen-printed shirts — Run One (sold out), Run Two ($30, S–XL) |
| `feet`  | Contact       | Instagram `@albumfolds`, email `fold@fold.gallery` |

Other pieces:

- `header.hero` — logo, intro copy, and `#navmap`, the fold-map nav (toggle `#navToggle`,
  panel `#navPanel`, buttons `.navmap-btn` with `data-target` = article id)
- `#lightbox` — full-size character viewer, opened from `.gchar`, closed by `#lb-close`
- Favicon + Apple touch icon are inline data URIs in `<head>`

### Cast card anatomy

Each `.gchar` carries four attributes the lightbox reads:

```html
<div class="gchar" data-i="…" data-name="Owl Samurai"
     data-num="…" data-numimg="data:image/png;base64,…"  <!-- hand-drawn number -->
     data-full="data:image/jpeg;base64,…">               <!-- full-size artwork -->
  <img class="pic" src="data:image/jpeg;base64,…">       <!-- thumbnail -->
</div>
```

So a new character needs **three** images: thumbnail (`.pic` src), full size (`data-full`),
and the drawn number (`data-numimg`). Add cards in cast order and keep `data-i`/`data-num`
sequential.

## Conventions

- Four `<style>` blocks and four `<script>` blocks, all inline. Keep it that way —
  no external files, no CDN, no build tooling
- Vanilla JS in ES5 style (`var`, `Array.prototype.slice.call`, IIFEs). Match it
- Class prefixes: `lf-*` = the interactive fold demo in `#head`; `tee-*` = shirts;
  `nm`/`navmap-*` = nav; `lb-*` = lightbox
- `aria-expanded` / `aria-hidden` are kept in sync by the scripts — preserve that when
  editing interaction code

## Workflow — preview first, always

Never edit `index.html` directly in response to a request, and never commit without
Rom's explicit go-ahead. The loop is:

1. **Preview.** Copy `index.html` to `preview.html` (gitignored), make the change there,
   and `open preview.html` so Rom can look at it in the browser. Say in a line or two
   what changed and where. Then ask whether to publish. The `/preview` command does this
2. **Approve.** Rom looks and says yes, no, or asks for a tweak. Tweaks go back into
   `preview.html` — the live file stays untouched
3. **Deploy.** On a yes: promote `preview.html` over `index.html`, delete the preview,
   commit with a plain-English summary, `git push origin main`. The `/deploy` command
   does this
4. Pages rebuilds in a minute or two. Hard-refresh (⌘⇧R); favicons cache stubbornly

If something looks wrong after a deploy, `git revert HEAD` + push puts the previous
version back — nothing is ever lost.

Rom can still commit through **GitHub Desktop** if he prefers; the two don't conflict.

If the padlock is missing on fold.gallery, that's a repo setting, not the code:
**Settings → Pages → Enforce HTTPS**.

## Known niggles

- `.DS_Store` is tracked in the repo and shouldn't be
- Git LFS is configured in `.git/config` but nothing currently uses it
- Everything in one file means every image change rewrites the whole 11.5 MB blob in git
  history. If the repo gets uncomfortably large, the fix is splitting images into an
  `assets/` folder and referencing them by path
