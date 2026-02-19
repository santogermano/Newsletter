# Newsletter Generator

RSS Newsletter Generator — a single-file web application for generating styled, email-ready HTML newsletters from RSS/Atom feeds with Outlook compatibility.

## Architecture

- **Single-file SPA**: The entire application lives in `Newsletter Generator.html` (~4,800 lines of embedded HTML, CSS, and vanilla JavaScript)
- **No external dependencies**: No frameworks, libraries, build tools, or package managers
- **Browser-native**: Open the HTML file directly in a browser to run

### Code Organization

The JavaScript is organized into 15 labeled sections:

1. **STATE** — Configuration object and data management (`config`, `allArticles`, `DEFAULT_CONFIG`)
2. **HELPERS** — Utility functions (`slugify`, `cleanText`, `truncateWords`, `debounce`, `updateConfig`)
3. **MODE & DOCK** — Simple/Advanced UI mode switching and layout docking
4. **HEADER & TOC BIND** — Table of Contents binding and header configuration
5. **LAYOUT** — Grid and column layout management
6. **CURATE LIST (ADVANCED)** — Article selection, filtering, and editing
7. **SECTIONS BOARD** — Newsletter section management and drag-and-drop
8. **PREVIEW / WYSIWYG** — Live preview generation and display
9. **TYPOGRAPHY (LIVE APPLY)** — Font family, size, and styling controls
10. **INLINE VISUAL EDITOR (LIVE PREVIEW)** — In-place content editing
11. **EXPORT (Outlook safe)** — HTML generation with Outlook compatibility transforms
12. **BUILD** — Core HTML construction and template building
13. **RSS CORE** — Feed fetching (with CORS proxy fallbacks) and RSS/Atom parsing
14. **ENHANCED VISUAL EDITOR** — Full WYSIWYG editing system
15. **INIT** — Application initialization and event binding

### Key Patterns

- `$()` is a shortcut for `document.getElementById()`
- State is managed via a single `config` object with `DEFAULT_CONFIG` baseline
- `allArticles` array holds all fetched feed items
- `localStorage` (key `"tg42"`) persists configuration and article data
- Network requests use `Promise.any()` with multiple CORS proxy fallbacks
- DOM updates are batched through debounce mechanisms
- `updateConfig(key, value, opts)` — convenience helper: sets `config[key]`, calls `save()`, and optionally calls `queueBuild()` (pass `{rebuild:false}` to skip)
- `filterArticleListBySearch(query)` — DOM-only article search; never modifies `allArticles`
- `setPreviewWidth(mode)` — toggles preview iframe between `"desktop"` (full) and `"mobile"` (375px)

### Protected Functions (do not change — affect newsletter output)

These functions produce the newsletter HTML and must remain stable:
`buildCard`, `buildColumns`, `buildSections`, `buildHeader`, `buildToc`, `outerHTML`, `outlookSafeTransform`, `renderCommentBlock`, `fetchFeedText`, `parseRSS`

## Design System

The UI uses a CSS custom property design token system (cool indigo palette):

| Token | Value | Usage |
|---|---|---|
| `--bg` | `#f8f8fb` | App background |
| `--surface` | `#ffffff` | Card / panel backgrounds |
| `--surface-2` | `#f1f1f7` | Subtle secondary surfaces |
| `--border` | `#e4e4ef` | All borders |
| `--ink` | `#12121a` | Primary text |
| `--ink-2` | `#56566b` | Secondary text |
| `--ink-3` | `#9090a8` | Muted / hint text |
| `--accent` | `#4f46e5` | Indigo primary action |
| `--accent-hover` | `#4338ca` | Accent hover state |
| `--accent-soft` | `rgba(79,70,229,0.08)` | Accent tinted backgrounds |
| `--accent-ring` | `rgba(79,70,229,0.20)` | Focus rings |

Font: **Plus Jakarta Sans** (Google Fonts) — loaded with weights 400/500/600/700/800.

### Key CSS Classes

- `.section-title-row` — card section header row: icon + uppercase label (+ optional badge)
- `.section-title-text` — uppercase 11px label inside `.section-title-row`
- `.icon-wrap` — inline-flex wrapper for SVG icons, inherits color
- `.preview-width-group` / `.preview-width-btn` — desktop/mobile preview toggle button group
- `.search-row` — search input row with leading icon (used in Curate card)
- `.btn-group` — segmented button group (e.g. columns selector, mode switcher)

### Keyboard Shortcuts

- `Ctrl/Cmd+S` — triggers Download HTML
- `Escape` — closes Visual Editor modal, then disables Inline Edit if active

## Conventions

- **Functions/variables**: camelCase (`parseRSS`, `buildHTML`, `fetchFeedText`)
- **Constants**: UPPER_SNAKE_CASE (`DEFAULT_CONFIG`, `FONT_STACKS`, `OUTLOOK_BASE_PX`)
- **DOM IDs**: camelCase, prefixed by feature (`rssUrl`, `headerText`, `veEditTitle`)
- **CSS classes**: kebab-case (`.btn`, `.card`, `.article-item`, `.drag-over`)
- **CSS custom properties**: theming via `--ink`, `--accent`, `--surface`, etc.
- **Icons**: inline SVG only (no icon font, no external sprite); use `stroke="currentColor"` so icons inherit text color

## Development

There is no build step, test suite, or linter configured. To work on the project, open `Newsletter Generator.html` in a browser and make edits directly to that file.

### Editing the file

- CSS is in a single `<style>` block (lines ~10–345)
- HTML structure is lines ~346–928
- JavaScript starts at line ~929 inside a single `<script>` block
- The file uses **Windows CRLF line endings** — when using the Edit tool, match exact whitespace from a Read first; avoid very long multi-line old_string patterns

### Testing changes

Open `Newsletter Generator.html` in a browser and manually verify:

- RSS feed fetching and parsing works
- Article curation and section management behaves correctly
- Preview rendering and inline editing function properly
- Exported HTML is valid and renders in email clients (especially Outlook)
- Settings persist across page reloads via localStorage
- Article search filters the list without altering article include/exclude state
- Desktop/mobile preview toggle constrains iframe width correctly
