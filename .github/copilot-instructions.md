# AI Copilot Instructions for Artist Commission Website

## Project Overview
This is a **single-page artist commission portfolio** for Jade (@jadentearl) built with vanilla HTML, CSS, and JavaScript. It showcases art commissions, merchandise, and portfolio items with interactive image galleries and pricing calculators.

## Technology Stack
- **HTML5** with responsive layout (no build process)
- **Tailwind CSS** (CDN: `https://cdn.tailwindcss.com`)
- **Vanilla JavaScript** (no frameworks)
- **External SDKs**: `/_sdk/element_sdk.js` and `/_sdk/data_sdk.js` (Element platform integration)
- **Custom fonts**: Poppins (Google Fonts) and Sailors serif (via cdnfonts)

## Architecture Patterns

### Data Structure
Content is defined in a **global `defaultConfig` object** (in `<script>` tag):
- `artist_name`: "@jadentearl"
- `section_title`: "Artist Commission Sheet"
- Commission styles (anime, chibi) with substyles, versions, and pricing
- Portfolio categories with image paths
- Merchandise fandom collections

### DOM Manipulation Strategy
Functions follow a **show/hide pattern** for interactive sections:
- `showStyle(style)`: Displays commission style (anime/chibi) and updates images
- `showSubStyle(subStyle)`: Reveals art quality options (lineart, colored, etc.)
- `selectVersion(style, version)`: Updates pricing labels and UI state
- `showPortfolioCategory(category)`: Tab-switching with element ID matching (e.g., `chibiPortBtn` → `chibiPortfolio`)
- **Collapsible sections** use `.hidden` class toggle (Terms, About Me, etc.)

### Image Gallery Pattern
Three overlapping character image containers at positions left: 0, 180px, 360px (lineart → base → rendered):
```html
<div class="character-image lineart-img">...</div>
<div class="character-image base-img">...</div>
<div class="character-image rendered-img">...</div>
```
Images have `.active` (scale 1.05, full opacity) and `.dimmed` (0.5 opacity) states.

### Modal System
Multiple modal types reuse the same `.modal` styles:
- **Lightbox**: Click image to enlarge (click outside closes)
- **YCH Examples**: Dynamically created grid of character pose examples
- **Merchandise**: Product detail modals

Close triggers: clicking backdrop, Escape key, or close button.

## Styling Conventions

### Color System
- **Primary accent**: `#f9c501` (gold/yellow) → all CTAs, highlights, theme colors
- **Text**: `#1a1a1a` (dark gray) for headings, `#2a2a2a` for body
- **Borders**: `#e0e0e0` (light gray) or theme color on hover
- **Gradients**: Yellow-to-gold buttons, subtle white-to-light-purple page backgrounds

### Class Naming
- Tailwind utility-first (no custom component classes)
- Custom `.theme-*` classes for reusable accent styling (`.theme-bg`, `.theme-text`, `.theme-border`)
- State modifiers: `.active`, `.dimmed`, `.hidden`, `.visible`

### Responsive Breakpoints
Standard Tailwind: `md:` (768px), `lg:` (1024px), `xl:` (1280px)
Character images container: `min-width: 700px` (scrollable on mobile)

## JavaScript Conventions

### Function Naming
- UI state changes: `show*()`, `toggle*()`, `close*()`, `open*()`
- Event handlers: inline or `window.onclick`, `addEventListener` for document-level events
- Lifecycle: `onConfigChange()` for Element SDK integration

### Event Delegation
Global click handler for modal backdrops:
```javascript
window.onclick = function(event) {
  if (event.target === modal) { closeModal(); }
};
```
Escape key listener for lightbox closing (reusable pattern).

### Element ID Conventions
- Buttons: `{name}Btn` (e.g., `chibiPortBtn`)
- Containers: `{name}Portfolio` or `{name}Content` (must match button naming)
- Modals: `{name}Modal`
- Example sections: `{poseId}-examples`

## Key Files & Their Roles
- **index.html** (2600+ lines): Monolithic file containing all UI, styles, and scripts
- **homepage.html**, **testrun.html**: Alternative versions or development branches
- **imgs/** directory: Organized by art style (anime style sheets, chibi variants)
- **CANVA/**, **COPY/**: Likely design drafts or alternate layouts

## Common Tasks & Patterns

### Adding a New Commission Style
1. Add entry to `defaultConfig.styles` object
2. Create display section in HTML with matching style name
3. Add `showStyle()` case and image paths
4. Update pricing in `updatePrices()` function

### Updating Portfolio Images
- Images referenced by path relative to site root (e.g., `imgs/anime style sheets/...`)
- No image processing; files served as-is
- Placeholder text fallback: "Placeholder" in `.character-image` divs

### Modifying Modal Behavior
- All modals use same backdrop click pattern
- Dynamically created modals (like YCH examples) use inline `style.cssText`
- Close button typically: `<span onclick="...remove()" style="...">`

## Element SDK Integration
The Element platform SDK is initialized if available (`window.elementSdk`):
- `defaultConfig` is passed to SDK initialization
- `onConfigChange()` callback updates section title dynamically
- `mapToCapabilities()` and `mapToEditPanelValues()` configure editor properties
- If SDK unavailable, site still functions (graceful degradation)

## Testing & Debugging Hints
- No build step; changes live-reload (serve with local HTTP server)
- Open browser DevTools console for JavaScript errors
- Image paths often cause 404s; check `imgs/` directory structure
- Modal z-index hierarchy: `.modal` (1000), dynamic YCH modal (3000)
- Use Tailwind's responsive prefixes to test mobile layouts

## When Making Changes
- Maintain semantic HTML (alt text on images, proper heading hierarchy)
- Keep inline styles minimal; prefer Tailwind classes
- Preserve SDK compatibility (don't rename `defaultConfig` properties)
- Test all interactive states (active/dimmed images, modal opening, collapsible sections)
- Update both style selectors when renaming portfolio categories
