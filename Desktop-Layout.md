# Desktop Layout

Triple-column dashboard layout for screens wider than 1100px.

---

## Layout Diagram

```mermaid
graph LR
    classDef header fill:#1a3a5c,stroke:#4a9,color:#fff
    classDef left fill:#2a4a3c,stroke:#4a9,color:#fff
    classDef center fill:#3a3a5c,stroke:#88a,color:#fff
    classDef right fill:#4a2a3c,stroke:#a88,color:#fff
    classDef bg fill:#1a1a2e,stroke:#333,color:#888

    subgraph Background["z: -10000 to -9998"]
        GFB["Gradient"]:::bg
        IMG["Cosmic Image"]:::bg
        STAR["Starfield"]:::bg
    end

    subgraph Header["Header Bar (full width)"]
        MENU["Menu"]:::header
        LOGO["BlockSight.Live"]:::header
        SEARCH["SearchBar"]:::header
        LANG["Language"]:::header
        HEALTH["HealthChip"]:::header
    end

    subgraph Left["Left Column (1/3)"]
        direction TB
        SC["SearchContainer"]:::left
        IP["InfoPanel"]:::left
        BD["BlockDetails"]:::left
        TD["TransactionDetails"]:::left
        AD["AddressDetails"]:::left
    end

    subgraph Center["Center Column (1/3)"]
        direction TB
        MEMPOOL["Mempool Blocks"]:::center
        NEXT["Next Block Card"]:::center
        LAST["Last Block Card"]:::center
        BUILT["Built Blocks (scroll)"]:::center
        LINKS["ChainLinks + Connectors"]:::center
        BEACON["HomeBeacon overlay"]:::center
    end

    subgraph Right["Right Column (1/3)"]
        direction TB
        ATH["AthStripBanner"]:::right
        TOGGLE["ToolBarToggle"]:::right
        BTC_P["BitcoinPrice (full)"]:::right
        CONG["CongestionGauge (full)"]:::right
        FEE["FeeGauge (full)"]:::right
        TOOLS["ToolBar (expandable)"]:::right
        JOY["ScrollJoystick"]:::right
    end

    subgraph Footer["Footer (fixed bottom)"]
        COPY["Footer"]
    end
```

---

## Desktop Characteristics

| Property | Value |
|----------|-------|
| Breakpoint | >1100px |
| Grid | `grid-template-columns: 1fr 1fr 1fr` |
| Left Column | Visible (`showLeftColumn: true`) |
| Widget Variant | `full` — all 3 gauges with tooltips on hover |
| Block Card Variant | `full` — large with fee rates, tx count, miner |
| ScrollJoystick | Visible (desktop only) |
| ToolBar | Full width, expandable, 5 tools |
| Header | Full navigation bar with search, language selector, health chip |

---

## Column Details

### Left Column — Search + Details
The left column serves two functions. At rest, it shows the search container where users can look up blocks, transactions, or addresses. When a result is selected, the InfoPanel switches to the appropriate detail view (BlockDetails, TransactionDetails, or AddressDetails) with full data display.

### Center Column — Blockchain Visualization
The centerpiece of BlockSight. A virtualized scrollable list renders block cards representing the Bitcoin blockchain in real-time:

- **Mempool section**: Projected mempool blocks (orange/amber), sized proportionally to fee density
- **Next Block**: The block being assembled, with a countdown timer
- **Last Block**: The most recently mined block, with elapsed timer
- **Built blocks**: Historical confirmed blocks, scrollable via the joystick or mouse wheel
- **Chain connectors**: Visual links between blocks with animated data flow

### Right Column — Gauges + Tools
Three real-time gauges stack vertically:
1. **BitcoinPrice** — live BTC/USD (or localized fiat) with 2-second refresh
2. **CongestionGauge** — 5-component weighted network congestion score
3. **FeeGauge** — recommended fees for next block and 6-hour target

Below the gauges, an expandable toolbar offers 5 analysis tools: supply metrics, general network stats, halving countdown, Monica metrics (custom analytics), and a BTC/fiat calculator.

---

**See also**: [[Tablet Layout]] | [[Phone Layout]] | [[Component Tree]]
