<div align="center">

# 7vndvlv

**The goal: one workspace for the entire investing loop.**

From global markets and live news channels to a broker-connected portfolio, price alerts and a bench to backtest and compare systematic strategies.

[![Downloads — Windows and Android, v0.1.0](docs/download-button.svg)](../../releases/latest)

In development · **v0.1.0** · Windows · Android · Source private

[**Features**](#features) · [**Demo**](#demo) · [**Architecture**](#architecture) · [**Decisions**](#decisions) · [**Roadmap**](#roadmap)

</div>

![The same screens on Windows and on a phone](docs/demo.webp)

*Five screens in the offline demonstration mode — the Windows window on the left, the
phone build on the right. The interface ships in French. The desktop is v0.1.0 as
published; the phone's centre button landed in the commit after it.*

> Built solo alongside a master's in finance, aiming for quantitative finance — the
> whole stack from the React client to the Python quant engine, in roughly 80 commits
> over June–July 2026.

---

## Features

What the app does today, on both targets:

|  |  |
|---|---|
| **Global market overview** | World map of exchanges, live indices by region, geopolitical risk band, continental panels, clocks |
| **Screener** | A fixed watchlist — 35 tickers across stocks, ETFs and crypto — sortable by change, market cap and distance from high, filterable by class. It reads a hard-coded list, not a universe scan; widening it is on the [roadmap](#roadmap) |
| **Live news streams** | Ten broadcast channels, up to six on screen at once, as rolling live streams |
| **News feed** | Headlines from four wire sources under the panels |
| **Portfolio tracking** | Allocation by asset class, risk metrics (beta, Sharpe, alpha), P&L, capital-gains tax estimate |
| **Charting** | Base-100 performance with RSI, MACD and volume overlays |
| **Price alerts** | Per-instrument thresholds, pushed over a Socket.IO gateway |

**Built, working, and not reachable from the interface.** The Python backtest engine
runs and returns eighteen statistics, `POST /api/algo/backtest` serves them, and a
React report renders the equity curve, the drawdown and the full trade log. What is
missing is the last link: the two components that call it — `AlgoDashboard` and a
strategy panel — are not wired into the router, and the Strategies screen you can
reach shows a static mock whose **Backtest** button has no handler. Same story for
the AI assistant and the in-app code editor. It is written, it is tested by hand, and
no click in the shipped app starts it.

The same React client ships to both targets. Three of the five screens above have a
phone layout of their own; the other two are listed under [Roadmap](#roadmap).

---

## Demo

The quickest way to judge any of it is to run it — and it runs with no backend at all.

On launch the app probes for its API; if nothing answers, it drops into a built-in
demonstration mode: frozen market data and a fictional portfolio. Nothing is
persisted, and a banner says so.

Builds are published under [Releases](../../releases/latest):

| Platform | File | First run |
|---|---|---|
| **Windows** — Intel / AMD | `x64` installer | SmartScreen → **More info** → **Run anyway** |
| **Windows** — ARM (Snapdragon, Surface Pro X) | `arm64` installer | same |
| **Android** | `.apk` | allow the source once, then install |

**What the shell dictates.** A packaged app is not a browser tab, and most of the
work went into the difference:

| | |
|---|---|
| Routing | through `#/` — a file loaded off disk has no server to rewrite URLs |
| Fonts | self-hosted, carried in the bundle — no network call |
| Native CSP | a host missing from `connect-src` is blocked **silently** in the package, never in dev |
| Session | cookie **and** Bearer token — the Tauri webview has no usable cookie jar |
| No backend | the app probes its API on launch; with no answer it drops to frozen data |
| Updates | minisign-signed manifest on Windows; on Android, an in-app version check |

---

## Architecture

One React client, two packaged targets, one API, and a Python process that is born
and dies with each request.

```mermaid
flowchart TB
    subgraph client["Client — one React 18 + TypeScript codebase"]
        ui["UI · ECharts · MapLibre GL<br/>hash routing · offline fallback"]
        win["Tauri v2 → Windows<br/>NSIS · MSI · minisign updater"]
        droid["Tauri v2 → Android<br/>APK · in-app version check"]
        ui --- win
        ui --- droid
    end

    subgraph api["API — NestJS"]
        rest["REST — 12 modules"]
        ws["Socket.IO — 2 gateways<br/>market ticks · price alerts"]
    end

    db[("MongoDB Atlas<br/>Mongoose schemas")]
    py["Python — one process per request<br/>yfinance · pandas · backtest engine"]
    yf(["Yahoo Finance"])

    ui -->|HTTPS| rest
    ui <-->|WebSocket| ws
    rest --> db
    ws --> db
    rest -->|spawn, JSON on stdout| py
    py -->|quotes, history| yf
```

**The API.** Twelve REST modules — `health`, `auth`, `market`, `portfolio`,
`cashflow`, `price-alerts`, `strategies`, `algo`, `news`, `ai`, `changelog`,
`countries` — plus two Socket.IO gateways, one pushing market ticks and one pushing
alert notifications. Auth is JWT with bcrypt and TOTP multi-factor; the guard is
still client-side, which is the honest limit listed under [Roadmap](#roadmap).

**The Python bridge.** NestJS keeps no long-running Python process. Each request that
needs market data or a backtest spawns a script, reads JSON off its stdout and parses
it; the process then exits. No queue, no broker, no shared state. The cost is roughly
200–400 ms of interpreter startup per request; the benefit is that a script that hangs
or crashes can never poison the API — and pandas never shares a heap with Node.

**The client.** One codebase, two shells. The same React build is wrapped by Tauri v2
for Windows and for Android, so a layout fix lands on both at once — and so does a
regression. What differs is deliberately small: the desktop carries a signed updater
the Android build cannot compile, and the phone swaps the MapLibre globe for a flat
map. Everything else, including the offline fallback, is shared code.

**Data on disk.** MongoDB Atlas holds users, portfolios, cash flows, strategies and
alert thresholds. Market data is never stored — it is fetched per request, cached in
memory for 30 seconds to 10 minutes depending on how fast the figure moves, and
dropped.

**The stack.**

| Layer | Technology |
|---|---|
| Shell | Tauri v2 (Rust) — Windows (NSIS/MSI) and Android (APK); minisign-signed desktop updater |
| Frontend | React 18 + TypeScript, Vite, ECharts, MapLibre GL |
| Real-time | Socket.IO — two gateways |
| Backend | NestJS — 12 REST modules |
| Auth | JWT, bcrypt, TOTP multi-factor (otplib, qrcode) |
| Database | MongoDB Atlas, Mongoose schemas |
| Quant / data | Python — yfinance, pandas, moving-average backtest engine |
| Packaging | Dockerfile for the API |

---

## Decisions

Each part was a choice. The ones that shaped the system — and the bugs that forced
some of them:

| Decision | Why | Alternative rejected | Trade-off accepted |
|---|---|---|---|
| **Tauri v2** for the shell | Installers under 4 MB and a minisign-signed update manifest, against roughly 150 MB for a Chromium-based shell | Electron | A Rust toolchain in the build chain, and one build per target architecture |
| **One-shot Python processes** | Each request spawns a script and reads JSON off its stdout; pandas and yfinance never share state with the Node process | A long-lived Python service | 200–400 ms of interpreter startup on every request |
| **Session in a cookie *and* a Bearer token** | The Tauri webview has no usable cookie jar — the cookie is dropped silently, with no error to catch | Cookie only | Two session paths to keep in sync, and a token reachable from JavaScript |
| **Hash routing and self-hosted fonts** | The bundled app must render with no network at all | Browser routing + Google Fonts | A `#` in every URL, and font files carried in the bundle |
| **No 3D globe on the phone** | MapLibre at 390 px drains the battery, and its gestures fight the scroll; mobile gets a flat, tappable map of the exchanges | One shared map component | Two map components to maintain instead of one |
| **`native-tls` over `rustls`** | `rustls` pulls in `ring`, which needs a clang toolchain on Windows; SChannel already ships with the OS | `rustls` | TLS behaviour follows the host OS store instead of being identical everywhere |
| **Updater excluded from the Android build** | `native-tls` uses the Windows cert store but drags in OpenSSL, which won't cross-compile for Android | A single cross-platform updater | Android checks GitHub Releases in-app instead of updating silently |
| **Offline mode by intercepting one fetch chokepoint** | Every HTTP call already funnelled through a single function, so offline support cost one modified function instead of 26 mocked components | Per-component mocks | Frozen fixtures age with every market day, and WebSocket traffic bypasses the chokepoint |

Three problems that took real debugging:

- **MongoDB Atlas unreachable on a local network** — the LAN's DNS server couldn't
  resolve `mongodb+srv` (`querySrv ECONNREFUSED`); fixed with a `DNS_SERVERS`
  override applied before bootstrap.
- **Memory exhaustion on a 512 MB host** — overlapping pandas polls stacked until
  the container hit its ceiling and restarted; fixed with a re-entrancy guard, a
  slower interval and a kill switch.
- **A shipped app that displayed nothing** — the bundled backend URL pointed at the
  visitor's own `localhost`, leaving them stuck at a login gate; fixed with a
  startup probe that drops into offline mode.

---

## Roadmap

In active development.

**Known limits**

- The backtest engine is unreachable from the interface — the report and the API call exist, the router and the button do not
- The API is not publicly hosted — the client expects one on localhost, or falls back to offline mode
- The screener reads a hard-coded list of 35 tickers; there is no universe scan behind it
- Quotes carry yfinance's lag; the real-time Socket.IO gateway only feeds price alerts
- No broker is connected — the **Paper** pill sends no orders; positions are held in the database
- Authorisation is client-side only — JWT + TOTP exist, but no server-side guard enforces them yet
- Two screens have no phone layout — **Open positions** and **Strategies** keep their two-column
  grid at 390 px, so the allocation donut covers the metric cards and the strategy list is crushed
- The AI assistant and in-app strategy editor are built but unrouted

**Planned**

- [ ] Route the backtest report and make the Strategies screen drive the engine
- [ ] A real screener universe instead of a fixed watchlist
- [ ] Phone layouts for the two remaining screens
- [ ] Thematic sector watch — the dial is in place, the feeds are not wired
- [ ] Server-side authorisation
- [ ] Broker integration for live orders

---

**The source code is private.** This repository hosts the installers, the update
manifest and this write-up.
