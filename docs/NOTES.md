# blog183 — development notes

## project overview

static hugo blog at [anir183.is-a.dev/blog](https://anir183.is-a.dev/blog), intended as a
content-first extension of the main portfolio at [anir183.is-a.dev](https://anir183.is-a.dev).

**stack**: hugo (extended, v0.141+) + hugo-brewm theme  
**fonts**: unbounded, bebasneue, ubuntu, jetbrains mono (self-hosted woff2, copied from portfolio)  
**colors**: portfolio dark/light palette mapped to theme's css variables  
**deploy**: github pages via github actions (with pagefind indexing)  
**status**: scaffolding / content seed

---

## 1. hugo-brewm theme analysis

### 1.1 philosophy (from README, codebase, and config)

the theme defines four core pillars:

| pillar | implementation |
|--------|----------------|
| **reader-first** | ~55kb gzipped total assets, no trackers, no cookie banners. privacy-respecting by default. fast on slow connections. font-display: swap everywhere |
| **inclusive** | graceful degradation. full functionality without javascript — works in lynx/w3m terminal browsers, rss readers, print. semantic html + wai-aria throughout |
| **scalable** | single-post blog → thriving digital garden. multi-author, multilingual (7+ languages), taxonomy-driven organization. pagefind search, giscus/fediverse comments, rss feeds per section |
| **frameworkless** | vanilla css + js. no npm, no build step, no framework lock-in. zero maintenance overhead. ~92kb css + ~35kb js, ~55kb gzipped total |

### 1.2 architecture

```
theme entry point: layouts/_default/baseof.html
  ├── partials/head.html → meta, css pipeline, js pipeline
  ├── partials/header.html → skip nav, logo, top nav (menu + search + i18n)
  ├── block: main (page content)
  │   ├── _default/single.html  (individual posts/pages)
  │   └── _default/list.html    (section listings, home page, taxonomies)
  ├── partials/footer.html → a11y panel, bionread, to-top, main-footer
  └── partials/a11y.html → accessibility console (client-side rendered)

css pipeline (partials/head/css.html):
  assets/css/typeface/*.css  →  @font-face + css variable declarations
  + layout/_default/*.css    →  baseof, single, list, home
  + typesetting/*.css        →  default typography, sectioning
  + component/*.css          →  a11y, background, breadcrumb, card, carousel,
                                 clock, column, contentinfo, fediverse, garden,
                                 hero, keyframe, link, logotype, mainfooter,
                                 marginpar, menu, pagefind, search, share, skipper, slide
  + optimize.css             →  animation / transition utilities
  + support.css              →  @supports queries
  + verbatim.css             →  code syntax highlighting
  + media/print.css          →  print stylesheet
  + custom.css               →  site-level overrides (empty in theme)

  → concatenated → minified → sha256 fingerprinted
  → ~92kb total, ~38kb gzipped

js pipeline (partials/head/js.html):
  accessibility.js  → a11y panel (colors, contrast, fonts, screen reader opts)
  default.js        → viewport adapt, collapse, accordion, logotype resize
  bionread.js       → bionic reading toggle
  extras.js         → extras (to-top, clock, etc.)
  qrcode.js         → colophon qr code
  
  → concatenated → minified → fingerprinted
  → ~35kb total, ~12kb gzipped
```

### 1.3 css variable system

the theme uses four core color variables, set via hugo `params.style`:

```css
:root {
  --ac: <accent>;     /* links, highlights, interactive elements */
  --bg: <background>; /* page background */
  --fg: <foreground>; /* text color */
  --mid: <muted>;     /* secondary text, borders, subdued elements */
  --off: <opposite>;  /* black (#000) in light, white (#111) in dark — auto */
}
```

these are set in the `baseof.css` template via hugo template syntax, so they live
in the compiled css and can be overridden by the a11y panel at runtime.

**contrast variants** (`prefers-contrast: more/less`) each have their own color
overrides. this gives 6 possible color schemes (light × 3 contrasts + dark × 3
contrasts), plus 4 color-blindness palettes applied as body classes.

### 1.4 font system

the theme provides ~10 font options, selected via `params.typeface`:

| role | css var | fonts available |
|------|---------|-----------------|
| roman (serif/heading) | `--rm` | EB Garamond (default), Cormorant, Crimson |
| sans (body) | `--sf` | LexicaUltralegible (default), Inter, Montserrat, Rosario |
| teletype (mono) | `--tt` | Inconsolata (default) |
| dyslexic | — | OpenDyslexic (mandatory, loaded via a11y panel) |
| icon | — | base-ui icon font (or minimal-ui subset) |

mode: `webSafe = false` loads the selected fonts from cdn (github raw or fonts.bunny).
`localHost = true` loads from `assets/css/typeface-local/` instead.
`webSafe = true` skips all font loading and falls back to system fonts.

### 1.5 accessibility panel (key differentiator)

the a11y panel is client-side rendered from `accessibility.js` and provides:

- **color scheme** — dark/light toggle (system preference + manual override, stored in localStorage)
- **contrast** — default / more / less (maps to css `prefers-contrast` media queries)
- **color palette** — deuteranopia, protanopia, tritanopia, monochrome (for code blocks with `.chroma`)
- **font size** — slider (8–12 range, maps to `--fontScale`)
- **baseline stretch** — slider (0.8–1.2 range, maps to `--baselineStretch`)
- **OpenDyslexic** — toggle that replaces `--rm`/`--sf`/`--tt` with OpenDyslexic
- **BionRead** — bionic reading mode
- **focus mode** — hides header, footer, comments for distraction-free reading
- **screen reader mode** — strips images, icons, external fonts for sr optimization

### 1.6 notable design decisions in the theme

- **golden ratio layout**: `--golden-ratio: 61.8%` for content width, `--canonic: 70.7%` for max content area. `--max-width: 58rem` caps it on wide screens.
- **marginpar width**: `--marginparwidth: 27vw` — sidenotes/margins in wide view.
- **void**: `--void: calc((29.289322vw - 1rem) / 3.14)` — horizontal padding that scales with viewport.
- **hyphenation enabled globally**: `hyphens: auto` with `hyphenate-after/before/lines` controls.
- **sticky header/footer**: header `position: sticky; top: 0`, footer `position: sticky; bottom: 0`.
- **scrollbar**: thin, themed via `scrollbar-color: var(--g18) transparent`.
- **no !important cascade**: the theme avoids !important. the `prefers-reduced-motion` userscript in `optimize.css` is the only place it's used (intentionally, as a kill switch).
- **mobile-first**: `@media (max-width: 640px)` collapses void, full-width margins. `@media (max-width: 480px)` further reduces spacing.

---

## 2. portfolio design analysis

reference: `/home/anir183/projects/portfolio/dev/docs/NOTES.md` (618 lines)

### 2.1 color palette

```
DARK MODE (default):
  --c-bg-0:        #131518   (near-black page bg)
  --c-bg-1:        #282c34   (secondary bg / cards)
  --c-bg-2:        #31353f   (tertiary bg)
  --c-neutral-0:   #abb2bf   (primary text)
  --c-neutral-1:   #5c6370   (secondary text)
  --c-neutral-2:   #21262d   (dark elements)
  --c-accent-0:    #eda792   (primary accent — warm peach)
  --c-accent-1:    #61afef   (secondary accent — blue)
  --c-border:      #454b59
  --c-shadow:      #282b34
  --c-success:     #98c379
  --c-warning:     #e5c07b
  --c-error:       #e86671
  --c-info:        #56b6c2

LIGHT MODE:
  --c-bg-0:        #cbcbcb   (warm light gray bg)
  --c-bg-1:        #bbbbae
  --c-bg-2:        #a7a7a7
  --c-neutral-0:   #383a42   (primary text)
  --c-neutral-1:   #6f7175
  --c-neutral-2:   #9c9fa5
  --c-accent-0:    #aa6704   (amber)
  --c-accent-1:    #0731ca   (blue)
  --c-border:      #696969
  --c-shadow:      #91917d
  --c-success:     #158514
  --c-warning:     #8b8600
  --c-error:       #e45649
  --c-info:        #0184bc
```

### 2.2 font stack

| family | usage | weight range | files |
|--------|-------|-------------|-------|
| **Unbounded** | headings, titles, branding, preloader, logo | variable 200–900 | Unbounded-VariableFont_wght.woff2 |
| **BebasNeue** | nav links, labels, captions, small-caps | single (regular) | BebasNeue-Regular.woff2 |
| **Ubuntu** | body text, paragraphs, descriptions | variable wght 300–700 + wdth | UbuntuSans-VariableFont_wdth,wght.woff2 + italic |
| **JetBrains Mono** | terminal, code, numbers, monospace | variable 100–800 | JetBrainsMono-VariableFont_wght.woff2 + italic |

all fonts: `font-display: swap`, self-hosted as woff2.

### 2.3 key design patterns (relevant to blog)

- **border treatment**: `border-b` always present but transparent → color transitions only (no 0→1px jump). apply this to theme's link/feed card borders.
- **selection**: accent-0 bg on neutral-0 text.
- **scrollbar**: thin, auto-hides after 4s idle. consider for blog (theme has thin scrollbar but no auto-hide).
- **accent hover**: left-to-right accent wipe on interactive elements. the theme uses animated border-image underline instead — simpler, lighter, keep it.
- **prefers-reduced-motion**: kill switch at css level (all transitions/animation → 0). theme's `optimize.css` already has this. portfolio additionally guards 18 js animation sites individually.
- **focus-visible**: outline in accent color. theme has this in link.css via border-color transitions.
- **mobile hamburger**: 3-span → X morph via css transitions. theme uses native `<details>` disclosure — keep it (frameworkless, works without js).

### 2.4 what the portfolio has that the blog intentionally won't

| feature | reason to skip |
|---------|----------------|
| GSAP (ScrollTrigger, SplitText) | content-first blog, no heavy scroll animations |
| preloader | no large images to preload |
| section-snap scroll | standard reading scroll behavior |
| parallax layers | no layered hero portrait |
| interactive terminal | not relevant to blog content |
| skills network / cube grid | portfolio-only portfolio features |
| page transition overlay | SPA-only concept, hugo is MPA |
| dot grid noise texture | adds visual weight without content purpose |

---

## 3. color mapping: portfolio → hugo-brewm

### 3.1 direct mapping

```
hugo-brewm var   portfolio light           portfolio dark
──────────────   ───────────────           ──────────────
--ac (accent)    #aa6704 (amber)           #eda792 (warm peach)
--bg (bg)        #cbcbcb (warm gray)       #131518 (near-black)
--fg (text)      #383a42 (dark gray)       #abb2bf (light gray)
--mid (muted)    #6f7175 (mid gray)        #5c6370 (muted gray)
```

### 3.2 full params.style config

```toml
[params.style]
  [params.style.light]
    ac = '#aa6704'
    bg = '#cbcbcb'
    fg = '#383a42'
    mid = '#6f7175'
    [params.style.light.less]
      ac = '#8b5803'
      bg = '#e0d6c9'
      fg = '#4a4d56'
      mid = '#8a8d92'
    [params.style.light.more]
      ac = '#d48205'
      bg = '#ffffff'
      fg = '#1a1a1a'
      mid = '#555555'

  [params.style.dark]
    ac = '#eda792'
    bg = '#131518'
    fg = '#abb2bf'
    mid = '#5c6370'
    [params.style.dark.less]
      ac = '#d4897a'
      bg = '#1a1d24'
      fg = '#9ca3b0'
      mid = '#4a505c'
    [params.style.dark.more]
      ac = '#f4c4b5'
      bg = '#000000'
      fg = '#e8eaed'
      mid = '#808080'
```

### 3.3 font mapping

```
hugo-brewm role   portfolio font     css var      weight strategy
────────────────   ──────────────     ───────      ──────────────
roman (serif)      UNBOUNDED          --rm         variable 200–900 → 400 body, 700 headings
sans (body)        UBUNTU             --sf         variable wght+wdth
teletype (mono)    JETBRAINS MONO     --tt         variable 100–800
n/a                BEBAS NEUE         --ff-label   single weight, used via custom css class
```

note: unbounded is a display sans — not a serif. but the theme uses `--rm` for headings,
and the portfolio uses unbounded for headings. we map it there for semantic correctness
in the theme's system. ubuntu remains the primary body font via `--sf`.

bebasneue has no natural slot in the theme's 3-font system. it will be added as a
custom `--ff-label` variable in `custom.css` and applied manually to specific elements
(nav links, labels) via a `font-family` override in `custom.css`.

---

## 4. implementation plan

### phase 1: `hugo.toml` — full configuration

- extend from current 4-line config to full params block
- `[params.style]` with portfolio color mapping (as above)
- `[params.typeface]` — `webSafe = true` (custom.css handles fonts)
- `[params.logo]` — portfolio logo SVGs
- `[params.author]` — name, email, bio
- `[params.comments]` — giscus config (later) or disabled initially
- `[params.search]` — pagefind enabled
- `[params.posts]` — colophon, related, share, justifying, noIndent
- `[params.feed]` — flowlines enabled (use portfolio-style abstract images)
- `[params.home]` — disable slide, disable listing (simple blog home)
- `[taxonomies]` — tags, categories, series
- `[markup]` — goldmark passthrough for math, highlight.js
- menus (`config/_default/menus.en.toml`) — home, posts, about, projects (→ portfolio)

### phase 2: fonts — copy + custom.css

- copy woff2 files from portfolio `static/fonts/` to blog `static/fonts/`
- create `assets/css/custom.css` with:
  - `@font-face` for all 4 fonts
  - `:root` overrides: `--rm` (Unbounded), `--sf` (Ubuntu), `--tt` (JetBrains Mono), `--ff-label` (BebasNeue)
  - `body` font-family: `var(--sf)` (Ubuntu)
  - headings font-family: `var(--rm)` (Unbounded)
  - code font-family: `var(--tt)` (JetBrains Mono)
  - custom `.label`, `.nav-link` classes using `var(--ff-label)`
  - scrollbar styling
  - selection colors
  - feed card hover refinements

### phase 3: visual identity refinements

- scrollbar matching portfolio (thin, auto-hide — see if theme's thin scrollbar suffices)
- selection colors (accent bg on fg text)
- border colors consistent with portfolio's `--c-border`
- override theme's `--g18` / `--g18s` (used for borders/shadows) to work with portfolio palette
- feed item card hover state accent treatment

### phase 4: content seed

- `content/_index.md` — home page with site description
- `content/post/re-introduction.md` — first blog post
- `content/about.md` — about page with bio and links
- `content/projects.md` — redirect / link to portfolio projects (placeholder)

### phase 5: build & deploy

- `.github/workflows/hugo.yml` — from theme's workflow template
- baseURL: `https://anir183.is-a.dev/blog`
- extended hugo + pagefind indexing step
- custom domain / subpath deployment on github pages

---

## 5. font setup detail

### 5.1 files to copy

from `/home/anir183/projects/portfolio/dev/static/fonts/`:

```
fonts/
├── unbounded/
│   └── Unbounded-VariableFont_wght.woff2
├── bebas_neue/
│   └── BebasNeue-Regular.woff2
├── ubuntu_sans/
│   ├── UbuntuSans-VariableFont_wdth,wght.woff2
│   └── UbuntuSans-Italic-VariableFont_wdth,wght.ttf    (no woff2 italic — consider generating)
├── jetbrains_mono/
│   ├── JetBrainsMono-VariableFont_wght.woff2
│   └── JetBrainsMono-Italic-VariableFont_wght.ttf       (no woff2 italic — consider generating)
```

note: portfolio has only ttf for italic variants of ubuntu and jetbrains. for hugo,
woff2 is strongly preferred. options:
- generate woff2 from ttf via `ttf2woff2` (same tool portfolio uses in `optimize-fonts.js`)
- serve the ttf files as fallback (browsers accept ttf but larger transfer)

### 5.2 @font-face examples for custom.css

```css
/* unbounded — headings */
@font-face {
  font-family: 'Unbounded';
  font-style: normal;
  font-weight: 200 900;
  font-display: swap;
  src: url('/fonts/unbounded/Unbounded-VariableFont_wght.woff2') format('woff2');
}

/* bebasneue — labels, nav */
@font-face {
  font-family: 'BebasNeue';
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url('/fonts/bebas_neue/BebasNeue-Regular.woff2') format('woff2');
}

/* ubuntu — body */
@font-face {
  font-family: 'Ubuntu';
  font-style: normal;
  font-weight: 300 700;
  font-display: swap;
  src: url('/fonts/ubuntu_sans/UbuntuSans-VariableFont_wdth,wght.woff2') format('woff2');
}
@font-face {
  font-family: 'Ubuntu';
  font-style: italic;
  font-weight: 300 700;
  font-display: swap;
  src: url('/fonts/ubuntu_sans/UbuntuSans-Italic-VariableFont_wdth,wght.ttf') format('truetype');
}

/* jetbrains mono — code */
@font-face {
  font-family: 'JetBrains Mono';
  font-style: normal;
  font-weight: 100 800;
  font-display: swap;
  src: url('/fonts/jetbrains_mono/JetBrainsMono-VariableFont_wght.woff2') format('woff2');
}
@font-face {
  font-family: 'JetBrains Mono';
  font-style: italic;
  font-weight: 100 800;
  font-display: swap;
  src: url('/fonts/jetbrains_mono/JetBrainsMono-Italic-VariableFont_wght.ttf') format('truetype');
}

/* css variable overrides */
:root {
  --rm: 'Unbounded';
  --sf: 'Ubuntu';
  --tt: 'JetBrains Mono';
  --ff-label: 'BebasNeue';
}
```

---

## 6. theme update process

```sh
# pull latest theme changes
git submodule update --remote --merge themes/hugo-brewm

# if that conflicts:
git submodule deinit -f themes/hugo-brewm
git rm -r --cached themes/hugo-brewm
rm -R themes/hugo-brewm
git submodule add -f https://github.com/foxihd/hugo-brewm themes/hugo-brewm

# commit
git add themes/hugo-brewm
git commit -m "theme(hugo-brewm): update to latest"
```

## 7. local dev commands

```sh
# preview with minification (required for correct whitespace)
hugo serve --minify --gc --port=8080 --bind=0.0.0.0 --baseURL=http://localhost:8080

# build for production
hugo --minify --gc

# pagefind index
npx pagefind --site "public"

# clean build cache (when templates persist unexpectedly)
hugo mod clean

# preview exampleSite from theme
hugo serve --minify --gc --source themes/hugo-brewm/exampleSite --themesDir ../..
```

## 8. deployment

- github pages via github actions (copy workflow from `themes/hugo-brewm/.github/workflows/hugo.yml`)
- extended hugo binary required
- pagefind indexing step after hugo build
- baseURL: `https://anir183.is-a.dev/blog` (subpath under portfolio domain)
- future: may migrate to `blog.anir183.is-a.dev` subdomain

## 9. roadmap

### done
- [x] init hugo site with hugo-brewm theme as git submodule
- [x] analyze hugo-brewm theme philosophy, architecture, and configuration system
- [x] analyze portfolio design (colors, fonts, patterns, accessibility)
- [x] document color mapping and font strategy in this file
- [x] extend `hugo.toml` with full params block (style, typeface, menus, search, etc.)
- [x] create `config/_default/menus.en.toml`
- [x] copy woff2 files from portfolio to `static/fonts/`
- [x] create `assets/css/custom.css` with @font-face + css variable overrides
- [x] create `content/_index.md` (home page)
- [x] write first blog post (re-introduction)
- [x] create `content/about.md` (about page)

### pending
- [ ] create `content/projects.md` (placeholder → portfolio link)
- [ ] review and adjust feed card styling
- [ ] review and adjust single post layout
- [ ] test a11y panel functionality with portfolio colors
- [ ] test print stylesheet
- [ ] test rss feed output
- [ ] copy portfolio logo SVGs to `static/assets/branding/` and configure `[params.logo]`
- [ ] consider converting ttf italic fonts to woff2 via ttf2woff2

### phase 5 — deploy
- [ ] set up github actions workflow
- [ ] configure pagefind
- [ ] deploy to `anir183.is-a.dev/blog`
- [ ] test production build

### future
- [ ] giscus comments setup
- [ ] fediverse comments setup
- [ ] rss feed promotion
- [ ] portfolio → blog cross-linking in navbar

---

## 10. key reference files

| purpose | path |
|---------|------|
| hugo config | `hugo.toml` |
| custom css overrides | `assets/css/custom.css` |
| theme config example | `themes/hugo-brewm/exampleSite/config/_default/hugo.toml` |
| theme css pipeline | `themes/hugo-brewm/layouts/partials/head/css.html` |
| theme js pipeline | `themes/hugo-brewm/layouts/partials/head/js.html` |
| theme color system | `themes/hugo-brewm/assets/css/layout/_default/baseof.css` |
| theme base layout | `themes/hugo-brewm/layouts/_default/baseof.html` |
| theme single layout | `themes/hugo-brewm/layouts/_default/single.html` |
| theme list layout | `themes/hugo-brewm/layouts/_default/list.html` |
| theme a11y js | `themes/hugo-brewm/assets/js/accessibility.js` |
| portfolio notes | `/home/anir183/projects/portfolio/dev/docs/NOTES.md` |
| portfolio layout.css | `/home/anir183/projects/portfolio/dev/src/routes/layout.css` |
| portfolio fonts | `/home/anir183/projects/portfolio/dev/static/fonts/` |
| portfolio branding | `/home/anir183/projects/portfolio/dev/static/assets/branding/` |
| portfolio icons | `/home/anir183/projects/portfolio/dev/static/assets/icons/` |
