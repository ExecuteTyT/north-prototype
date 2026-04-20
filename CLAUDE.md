# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

NORTH is a Russian-language marketing landing page for a company that sells modular container houses (дом / баня / бизнес-модуль). The repository holds a single hand-written HTML prototype plus hero/scenario/case images — there is no build system, package manager, or framework. All copy, labels, and UI strings are Russian; currency is formatted as `toLocaleString('ru-RU') + ' ₽'`.

## Layout

- [index.html](index.html) — the entire site at the repo root: inlined `<style>` block (design tokens, per-section CSS, media queries) and a small `<script>` at the bottom (nav scroll state, `IntersectionObserver` reveal animations, FAQ accordion, configurator price). ~1560 lines; everything lives here.
- [images/](images/) — `hero.png`, `scenario-house.png`, `scenario-business.png`, `scenario-bath.png`, `case-karelia.png`. Referenced from HTML as `./images/<name>.png`.

## Running the prototype

Open [index.html](index.html) directly in a browser, or serve the repo root with any static server (e.g. `python -m http.server 8000`). The layout is also compatible with GitHub Pages — deploy the root of the default branch and it renders as-is.

## Photo / placeholder state

The prototype intentionally mixes real photos with labelled placeholders to signal "work in progress" honestly. Current split:

- **Real photos (5):** hero (ночной дом), scenarios Дом / Бизнес / Баня, case Карелия.
- **Placeholders (6):** configurator stage (reserved for a 3D model, labelled `3D · Модель · React Three Fiber`), and all five landscape cards (labelled `AI-рендер · <регион>`).

When replacing a placeholder with a real asset: remove the entire `<div class="ph">…</div>` block and drop in a single `<img src="./images/…">`. Do not leave `.ph` and `<img>` side-by-side in the same container.

## Working inside the HTML

The prototype is a stack of `<section>` blocks in semantic order: `hero` → `manifesto` → `scenarios` → `configurator` → `landscapes` → `honest` (FAQ) → `process` → `case` → `cta` → `footer`. Section boundaries are marked with HTML comments (`<!-- HERO -->` etc., starting around [index.html:1119](index.html#L1119)). When editing or adding sections, respect these conventions:

- **Design tokens live in `:root`** at [index.html:11](index.html#L11). Color palette (`--ink`, `--paper`, `--wood`, `--rust`, `--rust-bright`), type stacks (`--f-serif` = Fraunces, `--f-sans` = Manrope, both loaded from Google Fonts as variable fonts), and responsive spacing (`--pad-section`, `--pad-gutter` using `clamp()`). Use the tokens — do not hardcode hex colors or px paddings.
- **Dark/light alternation:** sections alternate between the dark ink background (default `body`) and the paper background (`.manifesto`, `.honest`, `.process`, `.cta`). Keep this rhythm when inserting new sections.
- **Editorial accent:** titles wrap emphasized words in `<em>` styled with Fraunces' variable-font axes (`font-variation-settings: "opsz" 144, "SOFT" 100, "WONK" 1`) and often the rust color. `.section-label` is the small-caps eyebrow with a leading rule — reuse it rather than re-rolling.
- **Image-over-text readability:** `.scenario::after` at [index.html:429](index.html#L429) lays a 35%→0%→85% top-to-bottom ink gradient across every scenario card so the label and title stay legible on any photo. Reuse this pattern (not hand-tuned shadows) whenever you drop text over a photo.
- **Placeholder blocks** (`.ph` + `.ph-label`, defined at [index.html:47](index.html#L47)) stand in for unfinished visuals — currently the configurator stage and the five landscape cards. Sub-label convention: `AI-рендер · <subject>` for photo stand-ins, `3D · <model-stack>` for the configurator.
- **Scroll-triggered reveal:** any element with class `reveal` is observed by the `IntersectionObserver` at [index.html:1521](index.html#L1521) and gets `.in` added when it enters view. New content that should fade in needs this class, otherwise it will appear instantly.

## Interactive behavior

The `<script>` block starts at [index.html:1515](index.html#L1515):

- **Navbar:** `#nav` gains `.scrolled` past 30px scroll for the blurred glass state.
- **FAQ accordion:** each `.qa` container toggles `.open` on click of its `.qa-q` button.
- **Configurator pricing:** driven by `cfgState = { size, finish }`. Each option button declares `data-group` (on its parent `.cfg-options`), `data-val`, and `data-price`. On click, the selected button's price overwrites the corresponding `cfgState` key and the total re-renders. If you add a new control group (e.g. interior trim, glazing) you must both add a key to `cfgState` **and** extend the `if (groupName === ...)` chain at [index.html:1552](index.html#L1552) — the `env` group in the current markup is wired in HTML but its price never reaches the total because the handler only maps `size` and `finish`.

## Version label

The footer string "Прототип v1.3 · не production" at [index.html:1509](index.html#L1509) and the fixed corner tag `.proto-tag` at [index.html:1513](index.html#L1513) both carry the prototype version. Keep them in sync when bumping.
