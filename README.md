# DEPRECATED — superseded by the Web Dark Mode Bundle

> ⚠️ **This standalone userscript is deprecated as of v2.0 of the bundle.**
>
> All sites previously covered by this repo (and 10 others) are now consolidated into a single auto-updating Tampermonkey userscript:
>
> **https://github.com/BarnsAWS/AWS-DC-Full-Open-Source-Dark-Mode-Bundle**
>
> Direct install URL:
>
> `https://raw.githubusercontent.com/BarnsAWS/AWS-DC-Full-Open-Source-Dark-Mode-Bundle/main/web_dark_mode_bundle.user.js`
>
> ## Migration
>
> 1. Open Tampermonkey dashboard.
> 2. **Disable or remove** this script (otherwise two scripts will fight on the same page).
> 3. Open the install URL above and click **Install**.
> 4. Tampermonkey will auto-update from `main` going forward.
>
> ## Why the bundle exists
>
> - One install, one auto-update across all covered sites.
> - Shared Cloudscape v3.3 engine — every site gets the same standard at the same time.
> - Adding a new site is a one-file change to `SITES` in the bundle.
>
> The original README/ABOUT and userscript are retained below this notice for archival / reference.
>
> ---

# SAP Concur Community Dark Mode TamperMonkey Script

A Tampermonkey userscript that applies an AWS Cloudscape-aligned dark theme to the SAP Concur Community site at `https://community.concur.com` (Khoros / Lithium-based help and forum platform).

## What This Repository Contains

- `concur_community_dark_mode.user.js` — the Tampermonkey userscript.
- `README.md` — installation and verification instructions.
- `ABOUT.md` — purpose, design principles, and architecture notes.

## Behavior Coverage

The script follows the **AWS Cloudscape Dark Mode Standard v3.3**:

- forces a Cloudscape "Polaris Dark Mode" palette on the SAP Concur Community shell, the dark-blue top bar, the "SAP Concur Community" header, the "Sign In" button, the primary navigation tabs (HOME / PRODUCT FORUMS / GROUPS / RESOURCES / SUPPORT AND FAQS), the search input, the article body, the right-rail "Related Articles" and "Support Phone Number" cards, breadcrumbs, kudos/translate badges, the article author block (SAP Concur / Community Manager), and the footer
- preserves the full Cloudscape text hierarchy (emphasis headings, body text, helper, muted)
- applies semantic alert tints (red error, blue info, green success badge) and Amazon Orange / SAP-blue brand colors are preserved
- forces dark surfaces on dropdowns, popovers, modals (the Sign In modal), menus, listboxes, tooltips, and any Khoros `lia-*` framework surfaces
- runs the v3.3 `enforceDarkSurfaces()` JS pass which detects light surfaces via *both* `background-color` and `background-image` (so framework gradients with white stops are stripped), and routes them by luminance bucket to `--bg-secondary` (`#1b232d`) or `--bg-tertiary` (`#232b37`)
- includes the v3.3 sub-component coverage rules so card-internal `header` / `[class*="title"]` / `[class*="body"]` / `[class*="content"]` / `[class*="footer"]` drop to transparent and inherit the parent card surface — covers Khoros' `lia-component-message-view` and similar nested blocks
- attaches the v3.3 tight-loop `MutationObserver` on `style` attribute mutations, batched via `requestAnimationFrame`, so framework writes during interaction (focus, blur, hover, paginated forum loads) get corrected within ~16 ms
- includes the v3.1 inline-control rule so the "Search the Community" input and any Sign In form fields render as inset wells with a `#656871` border (passes WCAG 1.4.11)
- includes the v3.2 generalized in-content selected rule so the active nav tab (currently SUPPORT AND FAQS) gets the bottom-border underline pattern, while selected forum threads / list items get a `#001129` background with a 3px `#42b4ff` left-rail
- detects sticky/fixed top bars by position and forces them to `--bg-primary` (`#161d26`)
- pierces open shadow roots and re-applies the same stylesheet
- observes DOM mutations with a 250 ms main observer (class / ARIA / data-*) and a tight rAF observer (style only); runs delayed safety passes at 500 ms / 1500 ms / 3000 ms after `load`

## Install on Chrome

1. Install Tampermonkey: <https://chromewebstore.google.com/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo>
2. Open the Tampermonkey dashboard and confirm it is enabled.
3. Open the raw userscript URL and let Tampermonkey prompt for install:
   `https://raw.githubusercontent.com/BarnsAWS/Amazon-Concur-Community-Dark-Mode-TamperMonkey-Script/main/concur_community_dark_mode.user.js`
4. Click **Install**.
5. Visit `https://community.concur.com` and refresh once.

## Install on Firefox

1. Install Tampermonkey: <https://addons.mozilla.org/en-US/firefox/addon/tampermonkey/>
2. Open the Tampermonkey dashboard.
3. Open the raw userscript URL above and click **Install**.
4. Visit `https://community.concur.com` and refresh once.

## Verification Checklist

- [ ] Page body and main content render on `#161d26`.
- [ ] Top bar with the SAP Concur Community logo renders dark; the SAP logomark stays original blue (preserved via SVG hands-off).
- [ ] The "Sign In" button keeps its blue brand color and remains legible.
- [ ] Primary nav tabs (HOME / PRODUCT FORUMS / GROUPS / RESOURCES / SUPPORT AND FAQS) — selected tab is bright `#f9f9fa` with a `#42b4ff` underline; inactive tabs are muted `#8c8c94`.
- [ ] Search input ("Search the Community") renders as an inset `#0a0f15` well with a `#656871` border.
- [ ] Breadcrumbs (Home > Support and FAQs > "Why Can't I Sign In to the SAP Concur Tool?") — links are `#42b4ff`, the current page is `#8c8c94`.
- [ ] The article title "Why Can't I Sign In to the SAP Concur Tool?" is `#f9f9fa` emphasis.
- [ ] The author byline ("SAP Concur Community Manager") and date ("May 20, 2026") render readable on dark.
- [ ] The kudos counter (39) and Translate badge are on `#232b37` pill surfaces.
- [ ] Right-rail "Related Articles" and "Support Phone Number" cards render on `#1b232d` with `#424650` borders, and their internal sections (heading + body) drop to transparent.
- [ ] Inline links inside the article body ("Do I have a profile?", "Expired link", "2FA code error", "Password reset", "Invalid username or password", "Additional Information", "@concursolutions.com", "+1 855-895-4815", "See more information", "SAP Concur Support Overview") are all `#42b4ff` and lighten on hover.
- [ ] No card sub-component renders with a light surface.
- [ ] No element renders with a `background-image: linear-gradient(white, ...)` (open DevTools, search rendered styles for `linear-gradient`).
- [ ] No bright white flash on initial load.
- [ ] Navigating between articles / forum threads does not leave bright residue.

## Troubleshooting

- **Bright sections after load** — hard refresh (Ctrl+F5) and confirm Tampermonkey is enabled for `community.concur.com`.
- **The SAP logo block looks weird** — SVG content is intentionally untouched; if a particular logo container has a white inline background, file an issue with the outermost class name so the v3.3 sub-component block can be extended.
- **Sign In modal renders bright** — the modal is layered (`role="dialog"`) and should pick up `--bg-secondary` automatically; if not, confirm the `@match` covers the modal's host frame (some platforms render auth modals in iframes from a different subdomain).
- **community.concur.com loads with a non-Khoros skin in some regions** — file an issue if you see a wholly different CSS framework; this script targets the standard Khoros / Lithium build.

## Source References

- AWS Cloudscape Dark Mode Standard for LLM v3.3 (color tokens, sub-component coverage, background-image stripping, tight-loop style observer, contrast matrix, anti-patterns)
- Amazon Dark Mode LLM Playbook v1.3 (file/folder scheme, source-notation rules, repo init workflow)
