# Assignment 5: Audit a Website for Accessibility

Portfolio site for "Internet Andrea," audited and fixed for accessibility (WCAG 2.1 AA).

- **Repository:** `https://github.com/<your-username>/m5-hw5-lastname-firstname`
- **Live site:** `https://<your-username>.github.io/m5-hw5-lastname-firstname/`

&nbsp;
## Tools Used

- **axe-core** (Deque) — automated rule checks for structure, landmarks, headings, and form labels
- **WCAG contrast ratio formula** — calculated every text/background and UI-component color pair
- **Manual keyboard testing** — tabbing through the page to check focus visibility and control types
- Recommended cross-check: **Lighthouse** (Chrome DevTools → Lighthouse → Accessibility) and the **WAVE** browser extension

&nbsp;
## Audit Results — Before

| # | Issue | Where | WCAG |
|---|-------|-------|------|
| 1 | Page content not in landmarks (no `header`, `nav`, `main`, `footer`) | Whole page | 1.3.1 |
| 2 | Heading levels skip from `h1` to `h3` | About Me, form heading | 1.3.1 |
| 3 | Body text `#999` on white is **2.85:1** (needs 4.5:1) | Body | 1.4.3 |
| 4 | Nav links `#555` on `#333` is **1.69:1** | Nav | 1.4.3 |
| 5 | Footer text `#555` on `#333` is **1.69:1** | Footer | 1.4.3 |
| 6 | Hero heading `lightpink` over white snow is **~1.45:1** | Hero | 1.4.3 |
| 7 | Form fields have no `<label>`; placeholders disappear when typing | Form | 1.3.1, 3.3.2 |
| 8 | Placeholder text `#999` is **2.85:1** | Form | 1.4.3 |
| 9 | Input borders `#ccc` are **1.61:1** (needs 3:1 for UI components) | Form | 1.4.11 |
| 10 | "Send Message" is a `<div>` — not focusable, not announced as a button, can't be activated by keyboard | Form | 2.1.1, 4.1.2 |
| 11 | `outline: none` on the button and textarea hides keyboard focus | Form | 2.4.7 |
| 12 | Decorative 📞 emoji is read aloud ("telephone receiver") | Footer | 1.1.1 |
| 13 | No way to skip past the navigation | Top of page | 2.4.1 |

&nbsp;
## Fixes

### HTML (`index.html`)

1. **Landmarks:** Nav and hero wrapped in `<header>`; nav links changed from `<div class="nav">` to `<nav class="nav" aria-label="Main">`; About and form wrapped in `<main>`; their `div`s became `<section>`s labelled by their headings; footer `div` became `<footer class="footer">`. All original class names kept, so every `.nav`, `.content`, `.form-section`, `.footer` selector still works.
2. **Heading order:** Both `h3` headings changed to `h2` (the CSS selectors were updated to match — see below).
3. **Form labels:** Added a visible `<label for>` for Name, Email, and Message, with matching `id`/`name` attributes. Placeholders are kept as hints.
4. **Real button:** `<div class="submit-btn">` replaced with `<button type="submit" class="submit-btn">`, so it's focusable and works with Enter/Space.
5. **Form helpers:** Added `required` and `autocomplete="name"` / `autocomplete="email"` (WCAG 1.3.5).
6. **Decorative icon:** `aria-hidden="true"` on the 📞 emoji; the text "Call me anytime!" carries the meaning.
7. **Skip link:** Added "Skip to main content" as the first focusable element.
8. **Contact link:** The nav "Contact" link now jumps to the form (`#contact`).
9. Added a `<meta name="description">`.

### CSS (`styles.css`)

| Element | Before | After | Ratio |
|---------|--------|-------|-------|
| Body text | `#999` | `#333` | 12.63:1 (10.89:1 on the `#eee` form) |
| Nav links | `#555` | `#ffffff` | 12.63:1 |
| Footer text | `#555` | `#ffffff` | 12.63:1 |
| Hero heading | `lightpink` | `#ffffff` + 50% black overlay + text shadow | ≥ 3.95:1 even over pure white snow (large text needs 3:1) |
| Placeholder | `#999` | `#6b6b6b` | 5.33:1 |
| Input borders | `#ccc` | `#767676` | 4.54:1 |
| Button | `#0077cc` | `#005a9c` (hover `#003d6b`) | 7.14:1 |

Other CSS changes:

- `.content h3` → `.content h2` and `.form-section h3` → `.form-section h2` to match the new heading levels.
- Removed `outline: none` from `.submit-btn` and deleted the `textarea:focus { outline: none; }` rule.
- Added a 3px `:focus-visible` outline on links, buttons, and fields (white inside the dark nav).
- Added styles for `label` and the `.skip-link`. `<main>` has `tabindex="-1"` so the skip link reliably moves keyboard focus into it in every browser.
- `.submit-btn` gets `display: block; width: 100%` so the new `<button>` keeps the original full-width layout of the `<div>`.
- Both new `h2`s get `margin-top: 1em` to match the spacing the original `h3`s had.
- Fixed a pre-existing bug: `border: 0 1px solid ...` on `body` is invalid shorthand and was ignored; replaced with `border-style` / `border-width` / `border-color`.
- Added hover underline on nav links.
- On screens under 600px wide, the form takes full width instead of a cramped 65%.

&nbsp;
## Audit Results — After

- **axe-core:** 0 violations.
- **Contrast:** every text pair is ≥ 4.5:1 (hero heading ≥ 3:1 as large text); every UI component border ≥ 3:1.
- **Keyboard:** every interactive element can be reached with Tab, shows a visible focus ring, and the button submits with Enter/Space.

&nbsp;
## Stumbling Blocks

- axe's automated scan only flagged two rule types (landmarks and heading order). It counts a placeholder as an accessible name, so it didn't flag the missing labels, and it can't tell that a `<div>` styled as a button is broken. Manual review caught those.
- Contrast on the hero is tricky because the text sits on a photo whose brightness varies. A semi-transparent overlay set a guaranteed worst case to calculate against instead of guessing.
- Changing `h3` to `h2` meant updating the two CSS selectors that targeted `h3`, otherwise the headings would have lost their styling.
- Swapping the `<div>` for a real `<button>` changed its layout (buttons shrink to fit their text), so it needed `display: block; width: 100%` to look the same.
- The form has no backend (`action="#"`). Now that the button really submits, filling it in reloads the page with the entries in the URL. A real deployment would need a form service or a server.
