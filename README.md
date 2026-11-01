# PS Workspace — landing page

Static single-page website for **PS Workspace**, a free local-first office suite for Windows x64
(Word, Excel, PowerPoint, PDF, Markdown and HTML editors in one window, with a bring-your-own-key AI
panel). Live at <https://ps-workspace.online> (Vercel also serves the old
`ps-workspace.vercel.app` host, but the canonical URL is the `.online` domain).

No framework, no build step, no npm dependencies. One HTML file, an image folder and two root icon
files.

## Layout

```
index.html        the whole site: markup, CSS (<style>) and JS (<script>) in one file
assets/           24 files, ~754 KB — 21 WebP images + favicon.svg + favicon-192/512.png
                  (20 WebP shown on the page, og-image.webp is meta-only, favicon.svg is the tab icon)
favicon.ico       multi-size 16-256 icon at the web root, for browsers and crawlers that ask for /favicon.ico
apple-touch-icon.png  180x180 iOS home-screen icon
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

- **Vercel:** import the repo, leave the build command empty, output directory `.`. The project
  deploys to `ps-workspace.online` (add it under Settings → Domains), with
  `ps-workspace.vercel.app` kept as the underlying host.
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

Ten places hard-code `https://ps-workspace.online` (8 in `index.html`, 1 in each of the other
files), and all of them must be updated together:

1. `<link rel="canonical">`
2. `<meta property="og:url">`
3. `<meta property="og:image">`
4. `<meta name="twitter:image">`
5. the `WebSite` JSON-LD block — `"url"`
6. the `WebSite` JSON-LD block — `"logo"`
7. the `SoftwareApplication` JSON-LD block — `"url"`
8. the `SoftwareApplication` JSON-LD block — `"image"`
9. `<loc>` in `sitemap.xml`
10. the `Sitemap:` line in `robots.txt`

Social crawlers (WhatsApp, Facebook, X) and Google's favicon crawler cannot reliably read relative
image paths, which is why the image tags and the JSON-LD `logo` are absolute instead of `assets/...`.
Everything else on the page, including the favicon `<link>` hrefs, stays relative so the site keeps
working when opened from `file://`.

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
