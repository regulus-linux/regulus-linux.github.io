# regulus-linux.github.io

The website for [Regulus](https://github.com/maybejaishkhan/regulus), a Flatpak
that runs Windows apps as native Linux windows.

The site is built with [Astro](https://astro.build) and borrows the look of
GNOME's website stylesheet, cut down to just what's used here. It's fully
static, with no client-side framework.

## Pages

| Route | Source |
| --- | --- |
| `/` | `src/pages/index.astro` |
| `/download/` | `src/pages/download.astro` |
| `/enable-virtualization/` | `src/pages/enable-virtualization.astro` |
| `/faq/` | `src/pages/faq.astro` |
| `/troubleshooting/` | `src/pages/troubleshooting.astro` |
| `/contribute/` | `src/pages/contribute.astro` |

## Structure

```text
src/
├── components/          # reusable pieces
│   ├── Header.astro
│   ├── Footer.astro
│   ├── Button.astro
│   ├── ColoredRow.astro
│   ├── GitHubIcon.astro
│   ├── PageHero.astro   # compact page header (title + description)
│   ├── Prose.astro      # narrow reading column for text pages
│   ├── Split.astro
│   └── Tile.astro
├── sections/            # the landing page's sections, in order
│   ├── Hero.astro
│   ├── Introduction.astro
│   ├── Features.astro
│   ├── Explanation.astro
│   ├── Requirements.astro
│   └── Installation.astro
├── layouts/
│   └── Layout.astro     # <head> metadata, global styles, footer
├── pages/
│   ├── index.astro
│   ├── download.astro
│   ├── enable-virtualization.astro
│   ├── faq.astro
│   ├── troubleshooting.astro
│   └── contribute.astro
└── styles/
    ├── variables.css    # colours, sizes and light/dark values
    ├── reset.css
    └── globals.css      # base elements and shared pieces
public/
├── favicon.svg
├── tris-l.svg, tris-d.svg   # hero background (light and dark)
└── img/                     # app icon, hero art, feature icons
```

`index.astro` only lists the sections in order. The copy for each one lives in
its own file under `src/sections/`.

## Adding a page

Pages are plain `.astro` files under `src/pages/`. The file path is the URL, so
`src/pages/faq.astro` becomes `/faq/`. Text pages are just a `PageHero` plus a
`Prose` block:

```astro
---
import Layout from "../layouts/Layout.astro";
import PageHero from "../components/PageHero.astro";
import Prose from "../components/Prose.astro";
---

<Layout title="FAQ" description="Frequently asked questions.">
  <PageHero title="FAQ" description="Short answers to common questions." />
  <Prose>
    <h2>A question</h2>
    <p>The answer.</p>
  </Prose>
</Layout>
```

## Commands

| Command | What it does |
| :--- | :--- |
| `npm install` | Install dependencies |
| `npm run dev` | Start the dev server on `localhost:4321` |
| `npm run check` | Type and Astro diagnostics |
| `npm run build` | Build the site into `./dist/` |
| `npm run preview` | Preview the built site |

## Styles

Styles are split by where they belong:

- `variables.css` holds the design tokens (colours, sizes) and their light, dark
  and mobile values.
- `reset.css` is just the reset.
- `globals.css` covers base element styles and the few shared pieces: the
  centred `.container`, the `.narrow`, `.small-print` and `.warning` helpers,
  and the `.hero` band.
- Each component keeps its own styles in a `<style>` block, which Astro scopes
  to that component.

One thing to know about Astro scoping: a rule in a component can only match that
component's own elements, and Astro scopes every part of the selector, not just
the end. If a rule needs to reach slotted content or a child component, wrap that
part in `:global(...)`. `Prose.astro`, `ColoredRow.astro` and
`sections/Hero.astro` have examples.

Light and dark mode both come from `@media (prefers-color-scheme: dark)`, so
there's nothing to toggle.

## Components

The reusable pieces live in `src/components/`:

| Component | What it's for |
| :--- | :--- |
| `Button.astro` | Renders an `<a>` when you pass `href`, otherwise a `<button>`. Takes `variant` (`suggested` or `dark`), `square` and `standalone`; anything else you pass becomes an attribute. |
| `ColoredRow.astro` | The full-width tinted band, with a centred `.container` inside. |
| `PageHero.astro` | The compact page header: takes a `title` and optional `description`, and renders the site header above it. |
| `Prose.astro` | The narrow reading column for text pages. |
| `Split.astro` | Text on the left, image on the right, stacking on small screens. |
| `Tile.astro` | A single feature tile: icon, title, and a slot for the body. |
| `GitHubIcon.astro` | The GitHub mark used in the footer. |

Use `Button` rather than writing `<a class="btn">` yourself:

```astro
<Button variant="suggested" href="#install">Get started</Button>
<Button variant="dark" standalone href="https://example.com">Download</Button>
```

## Placeholder assets

The illustrations in `public/img/`, the `tris-*.svg` background, and the
`enable-virtualization` guide are stand-ins for now. Swap them in place once the
real ones are ready.

## Deploying

[`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) type-checks and
builds the site on every push and pull request, then publishes to GitHub Pages
on pushes to `main`.

Set the Pages source to GitHub Actions once, under Settings and then Pages.

The site is served from the root, `https://regulus-linux.github.io`, which is why
`astro.config.mjs` sets `site` but no `base`. The Regulus app points at
`https://jaish.dev/regulus/enable-virtualization`. If the site ever moves under
`/regulus/`, set `base: '/regulus'` in the config and update the internal links.
