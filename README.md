# 🚨 Stock Rating Check — Don't Trust the Buy Rating

> *Wall Street gives **Buy** ratings 90% of the time. This tool shows you what they're hiding.*

A single-file, zero-dependency HTML app that cross-checks analyst **Buy** ratings against hard data: insider trading filings, EPS beat history, price targets, and 52-week positioning. Enter any US-listed ticker and get an independent **PASS / WATCH / FAIL** verdict in seconds.

---

## 📸 Preview

```
┌─────────────────────────────────────────────────────┐
│  ● LIVE  ·  Market Open                              │
│                                                     │
│  Don't trust the                                    │
│  ┌──────────────────┐                               │
│  │  Buy rating      │  ← highlighted in blue        │
│  └──────────────────┘                               │
│  in your brokerage app.                             │
│                                                     │
│  ┌───────────────────────────────────────────┐      │
│  │  Enter a stock ticker                     │      │
│  │  [ NVDA                        ] Analyze →│      │
│  └───────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  NVDA   NVIDIA Corporation          $875.00         │
│  [ Technology ]                                     │
│                                                     │
│  ✅ PASS   Hard data supports the thesis   7 / 9   │
│  ████████████████████░░░░  (score bar)              │
└─────────────────────────────────────────────────────┘

  ┌──────────────────────────────────────────────┐
  │ 🔴 52-WEEK RANGE VS ANALYST PREDICTION       │
  │ Badge: RED FLAG                              │
  │ Near 52w high · Analyst projected +3% —     │
  │ nearly priced in                             │
  │ Source: Alpha Vantage OVERVIEW               │
  ├──────────────────────────────────────────────┤
  │ ✅ EPS BEAT / MISS HISTORY                   │
  │ Badge: CLEAR                                 │
  │ Last quarter beat by 8.2% · 4 consec. beats │
  │ Source: Alpha Vantage EARNINGS               │
  ├──────────────────────────────────────────────┤
  │ ✅ INSIDER ACTIVITY · 90 DAYS (SEC FORM 4)   │
  │ Badge: CLEAR                                 │
  │ 6 acquisitions vs 1 disposal (Form 4, 90d)  │
  │ Source: SEC EDGAR — free, no key             │
  ├──────────────────────────────────────────────┤
  │ ✅ ANALYST CONSENSUS PRICE TARGET            │
  │ Badge: CLEAR                                 │
  │ Consensus PT $1020 · +16% from $875         │
  │ Source: Alpha Vantage OVERVIEW               │
  ├──────────────────────────────────────────────┤
  │ 🟡 ANALYST BUY % — BIAS CHECK               │
  │ Badge: CAUTION                               │
  │ 91% Buy · 32 analysts                       │
  │ Skewed: no room left to upgrade              │
  │ Source: Alpha Vantage OVERVIEW               │
  └──────────────────────────────────────────────┘
```

---

## 🧠 How It Works

The app pulls data from **two free APIs** and runs it through a **5-signal scoring engine**:

```
                        ┌─────────────────────┐
   User enters ticker   │                     │
   ─────────────────►   │   Alpha Vantage API │ ── OVERVIEW (price, PT, ratings)
                        │   (2 calls/ticker)  │ ── EARNINGS (beat/miss history)
                        └─────────────────────┘
                                  │
                        ┌─────────▼─────────┐
                        │   SEC EDGAR API   │ ── Form 4 filings (insider trades)
                        │   (free, no key)  │    Last 90 days
                        └─────────┬─────────┘
                                  │
                        ┌─────────▼───────────────────────────┐
                        │         SCORING ENGINE (0–9)        │
                        │                                     │
                        │  Signal              Max pts        │
                        │  ──────────────────  ───────        │
                        │  52-Week Drift        +2            │
                        │  EPS Beat Streak      +3            │
                        │  Insider Activity     +2            │
                        │  Price vs PT          +2            │
                        │  Buy% Bias Check      ±1            │
                        └─────────────────────────────────────┘
                                  │
                    ┌─────────────┼─────────────┐
                    ▼             ▼             ▼
                 PASS (6–9)   WATCH (4–5)   FAIL (0–3)
```

---

## 📊 The 5 Signals Explained

### 1. 📍 52-Week Range vs Analyst Price Target (Drift Check)
Measures whether analysts' price targets have already been *priced in*. A stock sitting near its 52-week high with a target only 2–3% above current price is a red flag — the analyst hasn't updated their model.

| Condition | Badge | Score |
|-----------|-------|-------|
| Stock ≥80% up in range AND PT upside <5% | 🔴 Red Flag | +0 |
| Stock ≥60% up OR PT upside <10% | 🟡 Caution | +1 |
| PT upside >15% | ✅ Clear | +2 |
| Other | ⬜ Neutral | +1 |

---

### 2. 📈 EPS Beat / Miss History
Consecutive earnings beats are the most reliable signal of management execution. Misses signal guidance risk.

| Condition | Badge | Score |
|-----------|-------|-------|
| 3+ consecutive beats | ✅ Clear | +3 |
| 1–2 consecutive beats | ✅ Clear | +2 |
| Flat / inconsistent | ⬜ Neutral | +1 |
| Last quarter miss < -10% | 🔴 Red Flag | +0 |

---

### 3. 🔍 Insider Activity · 90 Days (SEC Form 4)
Executives filing Form 4 with the SEC must disclose all personal stock transactions. Net selling while analysts say "Buy" is one of the most reliable contrarian signals in finance.

| Signal | Badge | Score |
|--------|-------|-------|
| Net buying (acquisitions > disposals) | ✅ Clear | +2 |
| No net direction | ⬜ Neutral | +1 |
| Net selling (disposals > acquisitions) | 🔴 Red Flag | +0 |

> Data source: [SEC EDGAR EFTS](https://efts.sec.gov) — free, no API key required.

---

### 4. 💰 Analyst Consensus Price Target
Raw analyst price target vs. current 50-day moving average price.

| PT Upside | Badge | Score |
|-----------|-------|-------|
| >15% above current price | ✅ Clear | +2 |
| 0–15% above | ⬜ Neutral | +1 |
| Below current price | 🔴 Red Flag | +0 |

---

### 5. 🎯 Analyst Buy% — Bias Check
When ≥85% of analysts rate a stock a Buy, the consensus *cannot go higher* — there's no one left to upgrade. The only direction is down. This signal penalizes excessive bullishness.

| Condition | Badge | Adjustment |
|-----------|-------|------------|
| ≥85% Buy AND stock near 52w low | ✅ Clear (oversold) | +1 |
| ≥85% Buy (normal) | 🟡 Caution | −1 |
| 70–84% Buy | ✅ Clear | +0 |
| <70% Buy | 🔴 Red Flag | +0 |

---

## 🌐 Multi-Language Support

The UI fully supports **4 languages**, switchable in real time via the top-right toggle:

| Code | Language |
|------|----------|
| `EN` | English |
| `ES` | Spanish |
| `简` | Simplified Chinese (简体中文) |
| `繁` | Traditional Chinese (繁體中文) |

All signal names, descriptions, verdict text, error messages, and market status labels are translated. Language switching re-renders any active results instantly.

---

## ⏰ Live Market Status

The header badge shows real-time NYSE session status — no server required. It's calculated entirely in the browser using the `America/New_York` timezone via the `Intl` API.

```
● LIVE  ·  Market Open       ← 9:30am–4:00pm ET, Mon–Fri (green)
● LIVE  ·  Pre-Market        ← 4:00am–9:30am ET (amber)
● LIVE  ·  After Hours       ← 4:00pm–8:00pm ET (amber)
● LIVE  ·  Market Closed     ← Weekends / overnight (gray)
```

> Market status is only displayed in English mode. Non-English users see the live badge without NYSE hours.

---

## 🚀 Getting Started

### Requirements
- A **free Alpha Vantage API key** — [get one here](https://www.alphavantage.co/support/#api-key) (email, instant delivery)
- A modern web browser (Chrome, Firefox, Safari, Edge)
- No server, no install, no dependencies

### Setup (2 steps)

**Step 1** — Open `ratingcheck.html` in any text editor and find line ~213:
```javascript
const AV_KEY = "";   // <── PASTE YOUR FREE KEY HERE
```

**Step 2** — Paste your key between the quotes:
```javascript
const AV_KEY = "YOUR_KEY_HERE";
```

Then open the file in your browser. That's it.

---

## 📡 Data Sources

| Source | Endpoint | What It Provides | Cost |
|--------|----------|------------------|------|
| [Alpha Vantage](https://www.alphavantage.co) | `OVERVIEW` | Price, analyst ratings, PT, 52-week range | Free (25 calls/day) |
| [Alpha Vantage](https://www.alphavantage.co) | `EARNINGS` | Quarterly EPS vs estimates | Free (25 calls/day) |
| [SEC EDGAR](https://efts.sec.gov) | `EFTS search` | Form 4 insider filings (last 90 days) | Free, no key |

> **Rate limit:** Alpha Vantage free tier allows 5 calls/minute and 25 calls/day. The app adds a 1.5-second delay between calls to stay within limits. If you hit the rate limit, wait 60 seconds and try again.

---

## 🏗️ Architecture

This is a **zero-dependency, single-file app**. Everything — HTML, CSS, and JavaScript — lives in one `.html` file.

```
ratingcheck.html
├── <head>
│   ├── Google Fonts (Archivo + JetBrains Mono)
│   └── <style> — ~120 lines of CSS with CSS custom properties
│
├── <body>
│   ├── .lang-switcher       — EN / ES / 简 / 繁 toggle buttons
│   ├── .live-badge          — Animated red dot + market status
│   ├── h1 + .tagline        — Hero copy
│   ├── .signal-list         — Three hook bullets
│   ├── .search-card         — Ticker input + Analyze button
│   ├── #status              — Loading spinner / last-updated text
│   ├── #err                 — Error display panel
│   └── #results             — Output panel (hidden until analyzed)
│       ├── .co-card         — Symbol, name, price, sector, verdict chip
│       ├── .score-bar       — Animated 0–9 fill bar
│       └── #signals         — Dynamic signal cards (sorted by severity)
│
└── <script>
    ├── TRANSLATIONS{}       — i18n strings for all 4 languages
    ├── setLang()            — Language switcher + re-render
    ├── avGet()              — Alpha Vantage fetch wrapper
    ├── edgarInsider()       — SEC EDGAR Form 4 parser
    ├── analyze()            — Main orchestration + scoring logic
    ├── render()             — DOM builder for results panel
    ├── META{}               — Per-signal color/desc/source resolvers
    ├── VERDICT_MAP{}        — PASS/WATCH/FAIL → chip class + text
    └── updateMarketStatus() — NYSE hours calculator (runs every 60s)
```

---

## ⚠️ Limitations & Known Behavior

- **Price approximation:** To stay within the 2-call-per-ticker budget, price is derived from the `50DayMovingAverage` or `200DayMovingAverage` field in `OVERVIEW` (not the live quote). Values may differ slightly from real-time prices.
- **ETFs and ADRs:** SEC EDGAR Form 4 lookups may return no results for ETFs, ADRs, or foreign-listed companies. The signal will show "Neutral" rather than flagging this as a clean signal.
- **Free tier limits:** 25 Alpha Vantage calls/day means ~12 ticker lookups per day on the free plan.
- **Not financial advice:** This tool is for educational and research purposes only. Always verify with primary sources before making investment decisions.

---

## 🎨 Design System

The app uses a deep navy dark theme with a consistent token system:

| Token | Value | Used For |
|-------|-------|----------|
| `--navy` | `#03071e` | Page background |
| `--card` | `#091852` | Card backgrounds |
| `--sky` | `#4895ef` | Primary accent, highlights |
| `--green` | `#43d98e` | Clear / positive signals |
| `--amber` | `#f9c74f` | Caution signals |
| `--red` | `#f94144` | Red flag / negative signals |
| `--mono` | JetBrains Mono | Data values, labels, codes |
| `--display` | Archivo | Headings and body copy |

Signal cards animate in with a staggered `translateY` fade and are **sorted by severity** (Red Flags first, then Caution, Neutral, Clear).

---

## 📄 License

MIT — use freely, modify as needed. No attribution required.

---

*Not financial advice. Data sourced from Alpha Vantage and SEC EDGAR public APIs.*
