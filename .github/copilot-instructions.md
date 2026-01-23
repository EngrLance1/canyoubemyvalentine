# AI Copilot Instructions for Valentine's Project

## Project Overview
Single-page interactive Valentine's Day ask application. Fully self-contained HTML file with embedded CSS and JavaScript—no build process or external dependencies.

## Key Architecture Patterns

### Configuration-Driven Customization
All user-facing personalization is isolated in the `/* ===== EDIT HERE ===== */` section (lines 142-145):
- `herName` - recipient's name displayed in greeting
- `destination` - location for the date with emoji
- `placeImage` - URL for location photo

**Pattern:** Always preserve this section's position and format. When adding new customizable fields, maintain this comment block structure.

### Animation & Effects System
Three visual feedback layers, all self-contained in `<style>`:
1. **Floating background hearts** - `floatUp` keyframe + interval spawner (500ms)
2. **Confetti burst** - `fall` animation with random hue/rotation on YES/NO click
3. **Heart explosion** - `explode` animation with random directional vectors (--x, --y CSS vars)

**Pattern:** All animations use requestAnimationFrame-like setInterval spawning with automatic cleanup (remove after duration). Modify durations in keyframes and setTimeout values together.

### State-Driven UI Transitions
Three card states managed via `innerHTML` replacement:
1. **Initial prompt** - typing effect (char-by-char) + countdown timer
2. **Response acknowledgment** - shows based on YES/NO click
3. **Date reveal** - displays destination with image + auto-exit after 5s

**Pattern:** State machine is implicit (no explicit state variable). Each response updates `.card` innerHTML wholesale. Preserve element IDs (`#card`, `#name`, `#typed`, `#countdown`) as they're re-referenced across functions.

## Developer Workflows

### Testing Changes
- Open `index.html` directly in browser (no server needed)
- Modify EDIT section variables and refresh
- Interactive elements: **No Button** dodges on hover (uses `mouseenter` event listener)
- YES/NO click triggers full animation cascade

### Audio System
Two audio sources loaded from Pixabay CDN (lines 137-140):
- `#music` - background music (loops, plays on YES/NO)
- `#pop` - pop sound effect (plays on confetti)

Modify `src` attributes to use different audio URLs.

## Critical Patterns & Gotchas

### DOM Cleanup
Dynamically created elements (hearts, confetti) are explicitly removed after animation ends. Omitting `h.remove()` or `c.remove()` causes memory leaks. Always pair creation with `setTimeout(() => element.remove(), animationDuration)`.

### CSS Custom Properties for Dynamics
Heart explosion uses CSS variables for per-particle motion:
```javascript
h.style.setProperty("--x", `${Math.random()*300-150}px`);
```
These variables are consumed in the `explode` keyframe: `transform: translate(var(--x), var(--y))`. Changing the keyframe without updating variable names breaks animation.

### Countdown Timer Logic
Uses JavaScript `Date` constructor with hardcoded month index 1 (February):
```javascript
const t = new Date(now.getFullYear(), 1, 14);
```
This is 0-indexed (month 1 = February 14). Update manually for different dates.

### No Button Physics
The `.no` button repositions on `mouseenter` (not click). Position is calculated as random offset `±120px`. This is intentionally unconstrained—button can move off-screen.

## Cross-File Communication
None—single file contains all HTML, CSS, and JavaScript. All functions are global (no modules). State is implicit across function calls:
- `yes()` → `proceed(false)`
- `noBtn.onclick` → `proceed(true)`
- `proceed()` → orchestrates music, confetti, heart explosion, innerHTML swap

## Files & Extensibility
- `index.html` - entire application (277 lines)
- No separate CSS/JS files or assets (except external fonts & audio CDNs)

When refactoring: Extract CSS/JS into separate files only if project grows beyond 500 lines.

## Common Edits
| Task | Location |
|------|----------|
| Change recipient name | Line 143: `const herName` |
| Change date destination | Line 144: `const destination` |
| Change date image | Line 145: `const placeImage` |
| Adjust animation speed | CSS: modify `animation-duration` or keyframe `@` percentages |
| Change countdown date | Line 169: `new Date(year, month, day)` |
| Modify text content | HTML: lines 156-160 or JavaScript strings in `proceed()`/`reveal()` |
