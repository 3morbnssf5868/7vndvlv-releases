<div align="center">

# 7vndvlv

**One workspace for the entire investing loop.**

Global markets, live news, an order desk, price alerts — and a bench to backtest
and compare systematic strategies, including Python you write yourself.

[![Downloads — Windows and Android, v0.1.0](docs/download-button.svg)](../../releases/latest)

In development · **v0.1.0** · Windows · Android · Source private

</div>

![7vndvlv on Windows and on a phone](docs/hero.webp)

> Built solo alongside a master's in finance, aiming for quantitative finance —
> the whole stack, from the React client to the Python quant engine, the container
> it runs strategies in and the bridge to a broker, since June 2026.

*The interface ships in French. The screenshot above is from the current build, in
the offline demonstration mode; the published installer is v0.1.0.*

---

## Features

|  |  |
|---|---|
| **Global market overview** | World map of exchanges, live indices by region, geopolitical risk band, clocks, fold-out markets banner |
| **Execution desk** | Holdings, order ticket and order feed. Orders fill on a local simulator; balances are read from an Interactive Brokers paper account |
| **Portfolio tracking** | Allocation by asset class, beta, Sharpe, alpha, P&L, capital-gains tax estimate |
| **Live news** | Ten broadcast channels, plus headlines from four wire sources |
| **Newsletters** | A private inbox per account, with a morning digest written by Claude |
| **Price alerts** | Per-instrument thresholds, pushed over Socket.IO |
| **Strategy bench** | Three engines behind one screen — moving-average crossover, cross-sectional dual momentum, and **Python you write yourself**, sandboxed in a throwaway Docker container |

**It runs with no backend at all** — on launch the app probes its API, and drops
into an offline demo with frozen data if nothing answers.

---

## Download

| Platform | File | First run |
|---|---|---|
| **Windows** — Intel / AMD | `x64` installer | SmartScreen → **More info** → **Run anyway** |
| **Windows** — ARM (Snapdragon, Surface Pro X) | `arm64` installer | same |
| **Android** | `.apk` | allow the source once, then install |

Builds are published under [Releases](../../releases/latest).

---

**[Read the architecture →](docs/ARCHITECTURE.md)** — the request path, the
sandbox tested against six attacks, and the decisions behind them, with the
trade-off each one accepted.

**The source code is private.** This repository hosts the installers, the update
manifest and this write-up.
