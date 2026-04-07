# Component Tree

The full application hierarchy showing how BlockSight's ~90 components are organized from root to leaf.

---

## Full Application Hierarchy

```mermaid
graph TD
    classDef functional fill:#2d5a27,stroke:#4a9,color:#fff
    classDef system fill:#1a3a5c,stroke:#4a9,color:#fff
    classDef visual fill:#3d2a5c,stroke:#a4a,color:#fff
    classDef mobile fill:#5a3a1a,stroke:#ca6,color:#fff
    classDef context fill:#333,stroke:#888,color:#ccc

    subgraph Root["Root Layer"]
        GFB["GradientFallbackBackground<br/>z: -10000"]:::visual
        IB["ImageBackground<br/>z: -9999"]:::visual
        LI["LazyImage<br/>(picture element)"]:::visual
        SF["Starfield<br/>z: -9998"]:::visual
        PEB["ProductionErrorBoundary"]:::system
        TC["ToastContainer<br/>z: 700"]:::system
    end

    subgraph Providers["Context Provider Stack (7 providers)"]
        VP["ViewportProvider"]:::context
        DSP["DataStoreProvider"]:::context
        BDP["BlockchainDataProvider"]:::context
        PDP["PriceDataProvider"]:::context
        HDP["HealthDataProvider"]:::context
        SP["SearchProvider"]:::context
        IPC["InfoPanelProvider"]:::context
    end

    subgraph AppShell["Application Shell"]
        H["Header<br/>(desktop/tablet)"]:::system
        MH["MobileHeader<br/>(phone only)"]:::mobile
        AR["AppRouter"]:::system
        FC["FooterCopyright"]:::system
    end

    subgraph HeaderChildren["Header Children"]
        MD["Menu Dropdown"]:::system
        LB["LogoBeacon"]:::system
        SB["SearchBar"]:::functional
        LD["Language Dropdown"]:::system
        HC["HealthChip<br/>(+ tooltip)"]:::functional
    end

    subgraph DashboardPage["Dashboard — Main Page"]
        LC["LeftColumn<br/>(desktop only)"]:::system
        CC["CenterColumn"]:::system
        RC["RightColumn"]:::system
    end

    subgraph LeftCol["Left Column"]
        SC["SearchContainer"]:::system
        SR["SearchResults"]:::functional
    end

    subgraph CenterCol["Center Column"]
        BV["BlockchainVisualizer"]:::system
        TDB["TwoDBlockchain"]:::system
        VBL["VirtualizedBlockList"]:::system
        BC["BlockCard<br/>(5 types)"]:::functional
        BCL["BlockChainLink"]:::system
        CCN["ChainConnector"]:::system
        AO["AnimationOverlay"]:::visual
        HB["HomeBeacon"]:::system
    end

    subgraph RightCol["Right Column"]
        DD["DashboardData"]:::system
        TB["ToolBar"]:::system
        SJ["ScrollJoystick<br/>(desktop only)"]:::system
    end

    subgraph Widgets["Dashboard Widgets"]
        ASB["AthStripBanner<br/>z: 15"]:::visual
        TBT["ToolBarToggle"]:::system
        BP["BitcoinPrice"]:::functional
        CG["CongestionGauge"]:::functional
        FG["FeeGauge"]:::functional
    end

    subgraph Tools["ToolBar Children"]
        ST["SupplyTool"]:::functional
        GM["GeneralMetricsTool"]:::functional
        HT["HalvingTool"]:::functional
        MM["MonicaMetricsTool"]:::functional
        CT["CalculatorTool"]:::functional
    end

    subgraph MobileLayout["MobileDashboard (phone only)"]
        MBV["MobileBlockchainView"]:::mobile
        MBC["MobileBlockCard"]:::mobile
        MDP["MobileDataPanel"]:::mobile
        MSO["MobileSearchOverlay"]:::mobile
        MBS["MobileBottomSheet"]:::mobile
    end

    subgraph DetailPanels["InfoPanel Modes"]
        BD["BlockDetails"]:::functional
        TD["TransactionDetails"]:::functional
        AD["AddressDetails"]:::functional
    end

    %% Root connections
    IB --> LI
    PEB --> AppShell

    %% App connections
    H --> HeaderChildren
    AR --> DashboardPage

    %% Dashboard connections
    LC --> LeftCol
    CC --> CenterCol
    RC --> RightCol

    %% Right column
    DD --> Widgets
    TB --> Tools

    %% Center column
    BV --> TDB --> VBL --> BC
    VBL --> BCL
    VBL --> CCN
    VBL --> HB

    %% Mobile (replaces desktop)
    MH -.->|"replaces"| H
    MBV -.->|"replaces"| BV
    MBC -.->|"replaces"| BC
    MBS -.->|"replaces"| DetailPanels

    %% Detail panels (desktop)
    SC --> SR
    SR --> DetailPanels
```

---

## Legend

| Color | Type | Description |
|-------|------|-------------|
| Green | Functional | Displays data, has CEO specs and algorithms |
| Blue | System | Container, layout, navigation |
| Purple | Visual | Background, animation, decoration |
| Orange | Mobile | Phone-only replacement component |
| Gray | Context | React context provider |

---

## Architecture Layers

### 1. Root Layer
Three z-indexed backgrounds (gradient, cosmic image, animated starfield) create the visual atmosphere. A production error boundary wraps the entire application for graceful failure handling.

### 2. Context Provider Stack
Seven React context providers establish the data infrastructure. Each manages a specific domain: viewport dimensions, centralized data store, blockchain data, price data, health monitoring, search state, and info panel state. Components consume data through domain-specific hooks rather than prop drilling.

### 3. Application Shell
The shell renders the appropriate header (desktop `Header` or phone `MobileHeader`), the router, and a fixed footer. The router currently serves a single dashboard route.

### 4. Dashboard (3 columns)
- **Left**: Search container + detail panels (block, transaction, address). Hidden on tablet, replaced by bottom sheet on phone.
- **Center**: The blockchain visualizer — a virtualized scrollable list of block cards (mempool, next block, confirmed blocks), connected by chain links and animated connectors. Includes the HomeBeacon navigation overlay.
- **Right**: Three real-time gauges (price, congestion, fees), an expandable toolbar with 5 analysis tools, and a scroll joystick (desktop only).

### 5. Mobile Alternative
Phone layout (<=768px) uses dedicated mobile components: `MobileHeader`, `MobileBlockchainView`, `MobileBlockCard`, `MobileSearchOverlay` (full-screen blur), and `MobileBottomSheet` (swipe to dismiss for details).

---

**See also**: [[Desktop Layout]] | [[Tablet Layout]] | [[Phone Layout]] | [[Component Bible]]
