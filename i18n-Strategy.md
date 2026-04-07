# i18n Strategy

BlockSight supports **25 languages** with lazy loading, RTL layout support, and automatic language-to-fiat currency mapping.

---

## Languages

| Language | Code | Script | RTL | Fiat Currency |
|----------|------|--------|-----|---------------|
| English | en | Latin | No | USD (primary) |
| Spanish | es | Latin | No | ARS |
| Hebrew | he | Hebrew | Yes | ILS |
| Portuguese | pt | Latin | No | BRL |
| French | fr | Latin | No | EUR |
| German | de | Latin | No | EUR |
| Italian | it | Latin | No | EUR |
| Dutch | nl | Latin | No | EUR |
| Polish | pl | Latin | No | PLN |
| Turkish | tr | Latin | No | TRY |
| Vietnamese | vi | Latin | No | VND |
| Indonesian | id | Latin | No | IDR |
| Ukrainian | uk | Cyrillic | No | UAH |
| Chinese (Simplified) | zh-CN | CJK | No | CNY |
| Japanese | ja | CJK | No | JPY |
| Korean | ko | CJK | No | KRW |
| Hindi | hi | Devanagari | No | INR |
| Bengali | bn | Bengali | No | BDT |
| Thai | th | Thai | No | THB |
| Arabic | ar | Arabic | Yes | SAR |
| Russian | ru | Cyrillic | No | RUB |
| Malay | ms | Latin | No | MYR |
| Filipino | fil | Latin | No | PHP |
| Chinese (Traditional) | zh-TW | CJK | No | TWD |
| Farsi | fa | Arabic | Yes | IRR |

---

## Translation Volume

Each language has **~1,730 translation keys** across 3 namespaces:

| Namespace | Keys | Content |
|-----------|------|---------|
| `common` | ~809 | UI labels, buttons, navigation, status messages |
| `documentation` | ~829 | Help text, tooltips, feature descriptions |
| `portal` | ~92 | Customer portal specific labels |

---

## Lazy Loading

Translations are loaded on demand, not bundled. When a user selects a language:

1. Vite's dynamic import fetches only that language's JSON files
2. i18next loads the namespace into memory
3. All components re-render with translated strings via `useTranslation()`

This keeps the initial bundle small — only English is loaded at startup.

---

## Language-to-Fiat Currency Mapping

When a user switches language, the secondary fiat currency changes system-wide. A shared `useSecondaryFiat` hook maps the current `i18n.language` to a currency code and live FX rate. Every component that displays monetary values (BitcoinPrice, FeeGauge, Calculator, BlockDetails, TransactionDetails, AddressDetails) uses this hook.

For example: switching to Hebrew shows prices in both USD and ILS. Switching to Japanese shows USD and JPY.

The FX rates come from the backend's multi-currency exchange rate service, which fetches live rates for all 26 currencies.

---

## RTL Support

Hebrew, Arabic, and Farsi are right-to-left (RTL) languages. When one is selected:

- `document.dir` is set to `rtl`
- CSS layout tokens include RTL-aware variants
- A `useRTL()` hook provides components with the current direction
- The `useDocumentDirection()` hook manages the document-level attribute

---

## The LIMA Agent

All translations were produced by LIMA, a specialized AI translation agent. LIMA processes one language per session following strict guardrails:

- Never modify English source files
- Preserve all `{{placeholder}}` interpolation markers exactly
- Mark uncertain translations with `[REVIEW]` prefix
- Flag layout issues (text overflow, truncation) for human review
- Quality bar: natural to native speakers, not literal machine translation

---

## Admin Dashboard: English Only

The admin dashboard (`admin.blocksight.live`) is deliberately excluded from translation. It serves a single CEO/CTO user who operates in English. Translating admin-only interfaces would add 85+ files of maintenance burden with zero user benefit. The customer portal and public explorer are fully translated.
