# Tablet Layout

Dual-column responsive layout for screens between 769px and 1100px.

---

## Layout Diagram

```mermaid
graph LR
    classDef header fill:#1a3a5c,stroke:#4a9,color:#fff
    classDef center fill:#3a3a5c,stroke:#88a,color:#fff
    classDef right fill:#4a2a3c,stroke:#a88,color:#fff
    classDef hidden fill:#333,stroke:#555,color:#666

    subgraph Header["Header Bar (full width)"]
        MENU["Menu"]:::header
        LOGO["BlockSight.Live"]:::header
        SEARCH["SearchBar"]:::header
        LANG["Language"]:::header
        HEALTH["HealthChip"]:::header
    end

    subgraph Left["Left Column — HIDDEN"]
        SC["SearchContainer"]:::hidden
        IP["InfoPanel"]:::hidden
    end

    subgraph Center["Center Column (1/2)"]
        direction TB
        MEMPOOL["Mempool Blocks"]:::center
        NEXT["Next Block Card (compact)"]:::center
        LAST["Last Block Card (compact)"]:::center
        BUILT["Built Blocks"]:::center
    end

    subgraph Right["Right Column (1/2)"]
        direction TB
        BTC_P["BitcoinPrice (compact)"]:::right
        CONG["CongestionGauge (compact)"]:::right
        FEE["FeeGauge (compact)"]:::right
        TOOLS["ToolBar"]:::right
    end
```

---

## Tablet Characteristics

| Property | Value |
|----------|-------|
| Breakpoint | 769px - 1100px |
| Grid | `grid-template-columns: 1fr 1fr` |
| Left Column | Hidden (`showLeftColumn: false`) |
| Widget Variant | `compact` — smaller gauges, reduced text |
| Block Card Variant | `compact` — smaller with essential data only |
| ScrollJoystick | Hidden |
| Header | Same as desktop (may be snug at lower end) |

---

## Key Design Decisions

### Left Column Hidden
At tablet width, the left column (search results + detail panels) is hidden to give the blockchain visualization and gauges enough space. Search and detail viewing use the **MobileBottomSheet** component instead — the same swipe-to-dismiss overlay used on phone, extended to tablet via CEO directive.

### Compact Variants
All three dashboard gauges (BitcoinPrice, CongestionGauge, FeeGauge) switch to `variant='compact'`, which reduces their visual footprint. Block cards also use their compact variant with less metadata displayed.

### What Moves Where

| Desktop Feature | Tablet Behavior |
|----------------|----------------|
| Left column search | Header SearchBar (always visible) |
| InfoPanel details | MobileBottomSheet (tap to open, swipe to dismiss) |
| Full gauge display | Compact gauge variants |
| Full block cards | Compact block cards |
| ScrollJoystick | Hidden (scroll via touch/mouse) |
| ToolBar | Available but narrower |

---

**See also**: [[Desktop Layout]] | [[Phone Layout]] | [[Component Tree]]
