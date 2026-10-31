[README.md](https://github.com/user-attachments/files/34217051/README.md)
# PS Workspace — landing page

Static single-page website for **PS Workspace**, a free local-first office suite for Windows x64
(Word, Excel, PowerPoint, PDF, Markdown and HTML editors in one window, with a bring-your-own-key AI
panel). Live at <https://ps-workspace.vercel.app>.

No framework, no build step, no npm dependencies. One HTML file plus an image folder.

## Layout

```
index.html        the whole site: markup, CSS (<style>) and JS (<script>) in one file
assets/           22 files, ~745 KB — 21 WebP images + favicon.svg
                  (20 shown on the page, og-image.webp is meta-only, favicon is the tab icon)
sitemap.xml       one <loc>, because this is a single-page site
robots.txt        allows the site, blocks /_not-published/, points at the sitemap
_not-published/   never deploy this (see below)
```

## Run it locally

Either double-click `index.html` — `file://` works, there is no server-side code — or serve it:

```
cd "D:/ps workspace.html"
python -m http.server 8123
```

then open `http://127.0.0.1:8123`.

## Deploy

The repository root **is** the web root, so `index.html` must stay at the top level.

- **Vercel:** import the repo, leave the build command empty, output directory `.`. The current
  project deploys to `ps-workspace.vercel.app`.
- **GitHub Pages:** publish the repo root as the site source.

Nothing needs installing or compiling before either one.

## Editing the content

- Every feature claim on this page is copied from the shipped feature guide, edition 0.12.0
  (`PS-Workspace-Features.pdf`). That PDF sits beside the app source, not in this repo, so if you
  need it, ask for it rather than hunting here. If you add something new to the page, make sure the
  app really does it — this page used to advertise features that do not exist.
- Screenshots are real captures from the Windows build, converted to WebP. When you add one, always
  set `width` and `height` to the file's actual pixel size; that is what stops the layout jumping
  while the image loads.
- The page loads no web fonts. It uses the system Segoe UI stack, so type on a machine without
  Segoe UI will differ slightly — that is expected.

## If the domain ever changes

Six places hard-code `https://ps-workspace.vercel.app` (4 in `index.html`, 1 in each of the other
files), and all of them must be updated together:

1. `<link rel="canonical">`
2. `<meta property="og:url">`
3. `<meta property="og:image">`
4. `<meta name="twitter:image">`
5. `<loc>` in `sitemap.xml`
6. the `Sitemap:` line in `robots.txt`

Social crawlers (WhatsApp, Facebook, X) cannot read relative image paths, which is why those two
image tags are absolute instead of `assets/og-image.webp`.

## What the page sends over the network

| Request | When |
| --- | --- |
| `assets/*` | always, same origin — no external image or font host is used |
| `translate.google.com` | one `<script>` for the language pill. If it never loads (offline, blocked), the pill is replaced with a plain "Translation needs internet" note |
| `formspree.io/f/mnppnqoz` | only when a visitor presses **Send** on the feedback form |
| `gofile.io/d/XAlHes3L` | only when a visitor clicks **Download** |

There is no analytics script, no consent banner, no tracking pixel and no cookie. The one exception
is local storage: the "Join the community" button saves the entered name under the `ps_users` key on
the visitor's own device, so the badge list survives a reload. Nothing is uploaded, and names are
rendered as text, never as HTML.

## Do not publish `_not-published/`

That folder holds search-engine verification leftovers and one screenshot of a laptop screen that
shows a bystander's phone number. `robots.txt` disallows the path, but the folder should never be
uploaded in the first place. If you publish from git, add `_not-published/` to `.gitignore`.

## Responsive behaviour

Tested from 320 px to 1920 px with no horizontal overflow. The top navigation needs about 1064 px,
so it collapses into the hamburger menu at 1120 px and below; the editor cards step 1 → 2 → 3
columns and the checklist boxes 1 → 2 → 3 → 4. The comparison table scrolls inside its own container
instead of widening the page.
