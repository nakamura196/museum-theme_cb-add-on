# museum-theme_cb-add-on

CollectionBuilder-CSV Add-on: Museum Theme

This repository provides template files to add two new looks, a light/dark switch and a museum-style home page to a CollectionBuilder-CSV project. The files in this directory structure can be copied directly into a CB-CSV repository to add the new features.

*note: only compatible with CB-CSV out of the box, not other templates.*

[日本語版はこちら (README.ja.md)](README.ja.md)

## Museum Theme

Two looks for the whole site:

- **modern**: white and navy, sans-serif headings, the site name, menu and search on one row
- **museum**: warm paper, serif headings, the title banner above the menu

Both have a dark palette, and an optional switch in the menu lets visitors choose light or dark (it follows the operating system until they choose).
The styles work through [Bootstrap 5.3 CSS variables](https://getbootstrap.com/docs/5.3/customize/css-variables/), the same Bootstrap that CB-CSV uses, so CollectionBuilder's own components (buttons, modals, tables, the browse and search pages) follow the palette without changes to their templates.
Fonts are picked by the page language, so the same theme works for English, Japanese, Chinese and Korean collections.
No font files or libraries are added.

This cb-add-on contains:

| File | What it does |
| --- | --- |
| `_sass/_museum.scss` | museum palette (`--museum-*` variables) and component styles |
| `_sass/_modern.scss` | modern palette, dark palettes, one-row header, item-page layout |
| `_includes/head/color-mode.html` | sets light/dark before the page is drawn, so a dark page does not flash white |
| `_includes/color-mode-toggle.html` | the light/dark button in the menu |
| `_includes/museum-header.html` | the top of every page: menu and title banner, in the order of the look |
| `_includes/museum-nav.html` | the theme's menu bar, a copy of CB-CSV's `collection-nav.html` with the site name (modern) and the switch, without the small-screen logo |
| `_layouts/home-museum.html` | optional home page: large search box, example queries, image grid |

## How To Use

Add to CB-CSV project:

- Start a CollectionBuilder-CSV project (or use your existing one!).
- Download this add-on to your computer (click "Code" > "Download ZIP", then unzip package).
- Copy all contents in the add-on (all files and folders except "README.md", "README.ja.md" and "LICENSE") and paste into your CollectionBuilder-CSV project. The folder structure in this add-on should match up with your CB-CSV template, adding new files in the correct places (do not delete or overwrite anything in your existing CB project!).
- Make the small edits in [Changes to Existing Files](#changes-to-existing-files) below. A theme changes every page, so unlike other add-ons it needs a few lines in CB-CSV's own templates.

### Install as a Gem (alternative)

The same files are also packaged as a Jekyll theme gem, [cb-museum-theme](https://github.com/nakamura196/cb-museum-theme). Use it instead of copying if you want to update the theme by changing one version number.

- Add to `Gemfile`: `gem "cb-museum-theme", git: "https://github.com/nakamura196/cb-museum-theme.git", tag: "v0.1.0"`
- Add to `_config.yml`: `theme: cb-museum-theme`
- Run `bundle install`, then make the same [Changes to Existing Files](#changes-to-existing-files).

Jekyll uses a file in your site before the file of the same name in the gem, so you can still replace any one of the theme's files by copying it into your site.

### Choose the Look

In `_data/theme.yml`, add:

```yaml
theme-style: modern     # modern / museum
color-mode-toggle: true # light/dark switch in the menu; false to keep the site light
```

For the Latin fonts the theme is designed with (Source Serif 4 / Source Sans 3), set `font-cdn` in the same file:

```yaml
font-cdn: '<link rel="preconnect" href="https://fonts.googleapis.com"><link rel="preconnect" href="https://fonts.gstatic.com" crossorigin><link href="https://fonts.googleapis.com/css2?family=Source+Sans+3:ital,wght@0,400..700;1,400..700&family=Source+Serif+4:wght@400..700&display=swap" rel="stylesheet">'
```

Without it, the system fonts are used.
For Japanese, load Noto Serif JP and Noto Sans JP in the same way.

### Use the Museum Home Page

Optional. In `pages/index.md`, change the layout and add example queries:

```yaml
---
layout: home-museum
title: Home
permalink: /
banner-featured-image: true
search-examples:
  - map
  - letter
---
```

The text of `pages/index.md` is shown beside the search box.
Check the comments at the top of "_layouts/home-museum.html" for the options (for example `grid-items`, a list of objectids to show instead of all items).

## Changes to Existing Files

These edits connect the theme to CB-CSV: a few lines in three files.
CB-CSV's menu (`_includes/collection-nav.html`) and banner (`_includes/collection-banner.html`) are not edited; the theme uses its own copy of the menu, `_includes/museum-nav.html`.
To remove the theme, undo these edits. The add-on's files are then no longer used, and can be deleted.

1. `_sass/_custom.scss`, at the end:

   ```scss
   @use "museum";
   @use "modern";
   ```

2. `_includes/head/head.html`, on a new line right after `<meta charset="utf-8">`:

   ```liquid
   {% if site.data.theme.color-mode-toggle %}{% include head/color-mode.html %}{% endif %}
   ```

3. `_layouts/default.html`: put the look on `<html>`, and draw the top of the page with the theme's menu.

   Replace the first two lines:

   ```liquid
   <!doctype html>
   <html lang="en" class="h-100">
   ```

   with:

   ```liquid
   {%- assign style = site.data.theme.theme-style | default: "modern" -%}
   <!doctype html>
   <html lang="en" class="h-100" data-style="{{ style }}">
   ```

   Replace:

   ```liquid
       {% include collection-banner.html %}
       {% include collection-nav.html %}
   ```

   with:

   ```liquid
       {% include museum-header.html %}
   ```

If your site is not in English, also change `lang="en"` in step 3 to your language (for example `lang="ja"`). The theme picks its fonts from it.

The menu items still come from `_data/config-nav.csv`, as before.
If you have changed `_includes/collection-nav.html` yourself, make the same change in `_includes/museum-nav.html`; the same applies when a newer CB-CSV changes its menu.

## Customize

Try these in order.

1. **Settings** in `_data/theme.yml`: `theme-style`, `color-mode-toggle`, `font-cdn`.
2. **Variables** in `_sass/_custom.scss`, after the `@use` lines. Every colour, font and radius is a `--museum-*` variable listed at the top of `_sass/_museum.scss` (light) and `_sass/_modern.scss` (modern and dark). For example:

   ```scss
   :root { --museum-accent: #2a4f7a; --museum-accent-rgb: 42, 79, 122; }
   :root[data-bs-theme="dark"] { --museum-accent: #7da8d6; }
   :root { --museum-heading-font: var(--museum-sans); } // sans-serif headings in the museum look
   ```

3. **Extra rules** in `_sass/_custom.scss`, after the variables.
4. **Edit the add-on's files** directly. Keep a note of what you changed: a newer version of the add-on will not merge with your edits.

## Make Your Own Theme

This add-on is also an example of how to restyle a CB-CSV site while keeping CollectionBuilder's templates as they are.
The same steps work for your own look:

1. **Start from Bootstrap's variables.** Bootstrap 5.3 reads its colours, fonts and radii from CSS variables (`--bs-body-bg`, `--bs-primary`, `--bs-border-radius` ...). Set them once in a new Sass file, and every component follows. `_sass/_museum.scss` sets them from a short list of its own variables, so one palette drives the whole site.
2. **Put your styles in new files** under `_sass/`, and load them with `@use` at the end of `_sass/_custom.scss`, the file CB-CSV leaves for this. Do not edit `_base.scss` or the other CB-CSV Sass files.
3. **Hang alternative looks on an attribute.** Write the second look under a selector such as `:root[data-style="modern"]`, and set the attribute from `_data/theme.yml` (step 3 of Changes to Existing Files). For dark mode, use Bootstrap's own `[data-bs-theme="dark"]`.
4. **Add markup as new includes and layouts**, with names CB-CSV does not use. A new home page is a new layout that the page picks in its front matter, like `home-museum`.
5. **Keep edits to CB-CSV's templates to a few lines** that call your includes. When a whole include has to change, like the menu here, copy it under a new name and change the copy, instead of editing CB-CSV's file. Write the edits down in your README, as above, so they can be redone on a newer CB-CSV.

## Example

- [cb-theme-test](https://nakamura196.github.io/cb-theme-test/) ([repository](https://github.com/nakamura196/cb-theme-test)): CB-CSV's demo collection with this add-on, in the modern look with the light/dark switch.

## License

Code: MIT (see [LICENSE](LICENSE)).
Documentation (this README and README.ja.md): [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
