<div align="center">

# 7vndvlv

**A platform for trading with code.**

Write a Python strategy, run it through a sandboxed backtest engine and
send it to a real execution desk.

[![Downloads — Windows and Android, v0.1.0](docs/download-button.svg)](../../releases/latest)

In development · **v0.1.0** · Windows · Android · Source private

</div>

![7vndvlv on desktop and on a phone, side by side](docs/hero.webp)

*One React client, two shells — the portfolio on Windows and on a phone, in
the offline demonstration mode. Built solo alongside a master's in finance,
since June 2026.*

---

## Features

|  |  |
|---|---|
| **Global market overview** | World map of exchanges, live indices by region, geopolitical risk band, clocks, fold-out markets banner |
| **Execution desk** | Holdings, order ticket and order feed. Orders fill on a local simulator; balances are read from an Interactive Brokers paper account |
| **Portfolio tracking** | Allocation by asset class, beta, Sharpe, alpha, P&L, capital-gains tax estimate |
| **Charting** | Base-100 performance with RSI, MACD and volume overlays |
| **Live news** | Ten broadcast channels, plus headlines from four wire sources |
| **Newsletters** | A private inbox per account, with a morning digest written by Claude |
| **Price alerts** | Per-instrument thresholds, pushed over Socket.IO |
| **Strategy bench** | Three engines behind one screen — moving-average crossover, cross-sectional dual momentum, and **Python you write yourself**, sandboxed in a throwaway Docker container |
| **Strategy terminal** | Script files, an editor and a run column sliding over any page — same sandbox as the bench |

A user-written strategy reproduces the built-in engine to the cent — the same
execution loop runs both. Tested against six real attacks; see
[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

---

## Download

| Platform | File | First run |
|---|---|---|
| **Windows** — Intel / AMD | `x64` installer | SmartScreen → **More info** → **Run anyway** |
| **Windows** — ARM (Snapdragon, Surface Pro X) | `arm64` installer | same |
| **Android** | `.apk` | allow the source once, then install |

Builds are published under [Releases](../../releases/latest).

---

## Getting started

1. Download the installer for your platform, above
2. Open the strategy bench
3. Write a class with `on_bar(ctx)` — returns `LONG`, `FLAT` or `None`
4. Run it — same execution loop, same 18-statistic report, as the two
   built-in engines

---

**[Full architecture →](docs/ARCHITECTURE.md)** — the request-path diagram,
the complete decisions table, and the stack.

**The source code is private.** This repository hosts the installers, the
update manifest and this write-up.
