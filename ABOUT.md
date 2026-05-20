# About This Repository

## Purpose

This repository delivers a Tampermonkey userscript that re-themes the SAP Concur Community help/forum site at `https://community.concur.com` (built on the Khoros / Lithium platform) using the AWS Cloudscape "Polaris Dark Mode" token palette so it visually aligns with the rest of the Amazon-internal dark-mode toolset.

## Design Principles

- **Cloudscape palette as ground truth.** Surfaces, text tiers, links, borders, focus rings, and primary actions all use Cloudscape v3.3 tokens (`#161d26` body, `#1b232d` cards, `#0a0f15` inset controls/selected nav, `#42b4ff` link/accent). No hand-picked hex values.
- **Two-layer architecture.** CSS injected at `document-start` for an immediate dark paint; JS surface-enforcement passes as the safety net for inline styles, runtime injections, framework gradients, and shadow roots.
- **v3.3 sub-component coverage.** Card-internal `header` / `[class*="title"]` / `[class*="header"]` / `[class*="body"]` / `[class*="content"]` / `[class*="footer"]` drop to transparent so the parent card's `--bg-secondary` shows through. Mirrored across `card`, `Card`, `panel`, `widget`, `container`, `vds-card`, `adp-card`, `awsui_container`. Catches Khoros' `lia-component-*` content blocks and the article-detail "Related Articles" / "Support Phone Number" cards.
- **v3.3 background-image stripping.** Every dark `background-color` rule pairs with `background-image: none !important`. The JS `isLightSurface()` helper scans gradient stops and strips any with luminance > 200 (or named `white` / `#fff` / `#ffffff` / `#fefefe` / `#f8f8f8`).
- **v3.3 tight-loop style observer.** A dedicated `MutationObserver` on `attributeFilter: ['style']`, batched via `requestAnimationFrame`, catches framework writes within ~16 ms.
- **Hands off media + chart content.** Images, video, canvas, picture, and the SAP Concur logo SVG are excluded from color overrides. Article images embedded in posts are preserved.
- **Hands off the cascade.** No `filter: invert(1)`. Brand colors (SAP Concur blue logo, the bright blue "Sign In" header button, the green "Translate" success button, semantic alerts) are preserved.
- **Defensive against framework toggles.** `color-scheme: dark` and `data-color-scheme=dark` are re-asserted on every observer tick.

## Script Architecture

`concur_community_dark_mode.user.js` runs in seven phases:

1. **Framework hook.** Sets `color-scheme: dark` and `data-color-scheme=dark` on `<html>`. Adds `awsui-polaris-dark-mode` to `<body>` (harmless on non-Cloudscape hosts; lets shared rules cascade).
2. **CSS injection at `document-start`.** A single stylesheet covers nuclear text, transparent cascade, headings, links, inputs, buttons, primary CTAs, cards/widgets/containers, the v3.3 sub-component block, tables with zebra striping including `[role="row"]` / `[role="listitem"]` (which Khoros uses for forum threads), modals/menus/dropdowns/popovers, the left rail (Related Articles, Support Phone Number sidebar), the SAP Concur Community top bar, banners (info/error/warning), tabs (HOME / PRODUCT FORUMS / GROUPS / RESOURCES / SUPPORT AND FAQS), listbox options, in-content selected rows, sidebar selected rows, badges/pills/chips ("kudos", "translate"), breadcrumbs (Home > Support and FAQs > article title), progress bars, scrollbars, and stubborn inline-style overrides including v3.3 gradient-killers.
3. **JS `enforceDarkSurfaces()` pass.** Walks every non-media, non-input element, calls `isLightSurface()` (which scans both `backgroundColor` and `backgroundImage`), strips light gradients, and routes by luminance bucket to `--bg-secondary` (>140) or `--bg-tertiary` (≤140). Excludes all SVG descendants so the SAP Concur logo and any inline icons keep their original colors.
4. **Top-bar detection.** Combines selector- and position-based detection (`fixed`/`sticky`/`absolute` with `top < 100px`) to force the dark-blue Khoros top nav to `#161d26`.
5. **Inline control + selected row passes.** v3.1 + v3.2 — re-assert inset surfaces on the search input ("Search the Community"), Sign In modal fields, and selected breadcrumb / forum-row states. Sidebar variant detected via `closest()`.
6. **Tight-loop style observer.** v3.3 — flushed in one rAF frame.
7. **Main mutation observer + load-time safety net.** 250 ms debounce on `class` / `aria-*` / `data-*` mutations; explicit re-runs at 500 ms / 1500 ms / 3000 ms after `load`. Open shadow roots (Khoros uses some Web Components) receive the same stylesheet appended and `enforceDarkSurfaces()` runs inside them.

## Why This Repository Exists

community.concur.com is the SAP Concur help site Amazon employees land on whenever they search "concur 2FA" or "concur sign-in problem". It ships only a light theme (the Khoros default) which is jarring against the rest of an at-night dark-mode workflow. This script delivers a single, predictable, Cloudscape-aligned dark experience that survives SPA route changes between articles, group pages, and forum threads.

## Maintenance Notes

- Keep `concur_community_dark_mode.user.js` at the repository root so the GitHub raw URL is the canonical Tampermonkey install link.
- Cloudscape tokens are the source of truth. If Cloudscape revises the dark palette, update the constants block at the top of the userscript.
- Validate after any Khoros / Lithium platform upgrade — they occasionally rename internal CSS classes.
- The `@match` covers `community.concur.com` and `*.concur.com`. Concur's main app (`*.concursolutions.com`) uses Fiori UI on a different domain and is out of scope for this userscript.
- Do not add a global SVG recolor block. The SAP Concur logo, kudos icons, and forum thread icons depend on their original fills.

## Source References

- `AWS\Amazon AWS Dark Mode Standard for LLM.md` (v3.3) — palette, CSS rules, JS passes, sub-component coverage, bg-image stripping, tight observer, anti-patterns, framework hooks, contrast matrix.
- `Amazon Dark Mode LLM Playbook\AMAZON_DARK_MODE_LLM_PLAYBOOK.md` (v1.3) — naming, folder scheme, source-notation rules, repo initialization workflow under the `BarnsAWS` GitHub org.
