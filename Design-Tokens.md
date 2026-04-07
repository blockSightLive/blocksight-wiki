# Design Tokens

BlockSight uses a design token system with **400+ CSS custom properties** across **12 files** (~4,300 lines). Every visual value in the application — colors, spacing, typography, animation, shadows — is defined as a reusable token. No component uses hardcoded values.

---

## Philosophy

- **Dark-first**: Background `#0f172a`, white text. The entire UI assumes a dark canvas.
- **8px grid**: Every spacing value is a multiple of 4px (half-grid) or 8px (full-grid).
- **Bitcoin orange brand**: Primary accent `#f97316`, complemented by blue `#3b82f6` and purple `#a855f7`.
- **Glassmorphism**: Panels use `backdrop-filter: blur()` over semi-transparent backgrounds with subtle light borders.
- **Three font stacks**: Inter (UI text), JetBrains Mono (data values), DSEG7 (LCD countdown timers).

---

## Token Files

| File | Lines | What it defines |
|------|-------|-----------------|
| `colors.css` | 968 | 250+ color tokens: neutrals, brand, bitcoin, blockchain, status, glass, fee zones, congestion gauge |
| `typography-system.css` | 549 | Font imports, size scale (xs through 9xl), heading/paragraph/code styles |
| `animations.css` | 479 | Duration, easing curves, keyframes, block-found cascade, accessibility (prefers-reduced-motion) |
| `spacing-layout.css` | 390 | 8px grid, 40+ spacing values, breakpoints, z-index layers |
| `performance-optimization.css` | 326 | Shadow system (xs through 2xl), GPU acceleration hints, transition shortcuts |
| `borders-radius.css` | 286 | 60+ radius values, border widths, alpha borders, component-specific borders |
| `responsive-scale.css` | 259 | Height and width-aware `clamp()` scaling for different device sizes |
| `layout-grid.css` | 246 | Grid positioning, RTL support, layout helper tokens |
| `spatial-layout.css` | 224 | Z-index layers, spatial positioning tokens |
| `sizes-dimensions.css` | 193 | Icon sizes, touch targets (44px min), avatar sizes, component widths/heights |
| `reset.css` | 331 | CSS reset and normalize |
| `index.css` | 45 | Central barrel import for all token files |

---

## Naming Convention

Tokens follow a consistent prefix-based naming pattern:

```
--{category}-{variant}-{modifier}
```

Examples:
- `--color-neutral-900` -- darkest neutral background
- `--color-bitcoin-500` -- primary Bitcoin orange
- `--spacing-4` -- 1rem (16px), part of the 8px grid
- `--radius-lg` -- 8px border radius
- `--duration-300` -- 300ms animation duration
- `--font-size-base` -- 15px base text size
- `--shadow-md` -- medium elevation shadow
- `--z-index-modal` -- 1050, modal overlay layer

---

## Color Palette Highlights

| Token | Hex | Usage |
|-------|-----|-------|
| `--color-neutral-900` | `#0f172a` | Page background |
| `--color-neutral-0` | `#ffffff` | Primary text |
| `--color-bitcoin-500` | `#f97316` | Bitcoin brand accent |
| `--color-primary-500` | `#3b82f6` | Links, interactive elements |
| `--color-success-500` | `#22c55e` | Positive states, confirmations |
| `--color-error-500` | `#ef4444` | Errors, negative price changes |
| `--color-warning-500` | `#eab308` | Warnings, pending states |
| `--bg-glass-dark` | 75% opacity | Glass panel backgrounds |

---

## The Glassmorphism System

BlockSight's visual identity is built on glassmorphism — translucent panels floating over a cosmic space background. The design token system codifies this into reusable layers:

**Glass backgrounds** control panel transparency:

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-glass-dark` | `rgb(20 20 30 / 75%)` | Standard panel background |
| `--bg-glass-dark-heavy` | `rgb(20 20 30 / 85%)` | High-contrast panels (modals, tooltips) |
| `--bg-glass-dark-light` | `rgb(20 20 30 / 60%)` | Subtle overlays |
| `--bg-glass-dark-pill` | `rgb(30 30 40 / 60%)` | Pill-shaped badges |

**Glass borders** create the frosted edge effect:

| Token | Value | Usage |
|-------|-------|-------|
| `--border-glass-subtle` | `rgb(255 255 255 / 8%)` | Default panel border |
| `--border-glass-light` | `rgb(255 255 255 / 10%)` | Slightly visible edge |
| `--border-glass-medium` | `rgb(255 255 255 / 15%)` | Interactive elements |
| `--border-glass-strong` | `rgb(255 255 255 / 20%)` | Focused/active panels |

**Glass blur** levels for `backdrop-filter`:

| Token | Value | Usage |
|-------|-------|-------|
| `--blur-glass-sm` | `blur(8px)` | Subtle depth |
| `--blur-glass-md` | `blur(12px)` | Standard panels |
| `--blur-glass-lg` | `blur(16px)` | Modals, overlays |
| `--blur-glass-xl` | `blur(24px)` | Full-screen overlays |

A typical BlockSight panel combines all three layers:

```css
.panel {
  background: var(--bg-glass-dark);
  border: 1px solid var(--border-glass-subtle);
  backdrop-filter: var(--blur-glass-md);
  box-shadow: var(--shadow-glass-md);
  border-radius: var(--radius-lg);
}
```

---

## Case Study: Fee Zone Color Tokens

The FeeGauge component uses CEO-defined color zones to communicate urgency. These tokens show how business logic maps to visual design:

| Zone | Condition | Token | Color | Meaning |
|------|-----------|-------|-------|---------|
| Green | < 5 sat/vB | `--color-fee-zone-green-light` | `#22c55e` | Low fees, good time to transact |
| Yellow | 5-20 sat/vB | `--color-fee-zone-yellow-light` | `#eab308` | Moderate fees |
| Orange | 20-50 sat/vB | `--color-fee-zone-orange-light` | `#f97316` | High fees, Bitcoin orange |
| Red | > 50 sat/vB | `--color-fee-zone-red-light` | `#ef4444` | Very high fees, congested |

Each zone has three variants: `-light` (fill), `-dark` (border/accent), and `-shadow` (glow effect at 30% opacity). The thresholds come directly from the CEO's fee estimation specification — the colors are not arbitrary, they represent business-defined urgency levels.

---

## Why Tokens?

1. **Consistency**: Every component pulls from the same source of truth.
2. **Themability**: Changing `--color-bitcoin-500` updates every Bitcoin-branded element globally.
3. **Accessibility**: `prefers-reduced-motion` and `prefers-contrast` are handled at the token level.
4. **RTL support**: Layout tokens include RTL-aware variants.
5. **No magic numbers**: Code review rejects any hardcoded color, spacing, or font value.
