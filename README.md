<div align="center">

# 7vndvlv

**A platform for trading with code.**

Write a Python strategy, run it through a sandboxed backtest engine and
send it to a real execution desk.

[![Downloads — Windows and Android, v0.1.0](docs/download-button.svg)](../../releases/latest)

In development · **v0.1.0** · Windows · Android · Source private

</div>

![7vndvlv on desktop and on a phone, side by side](docs/hero.webp)

*The same React client, wrapped for Windows and for Android — the portfolio
shown here, in the offline demonstration mode.*

---

## Features

|  |  |
|---|---|
| **Portfolio & execution** | Holdings, order ticket and order feed — orders fill on a local simulator, balances read from an Interactive Brokers paper account. Base-100 performance charted with RSI, MACD and volume overlays |
| **Strategy engine** | Three engines behind one screen — moving-average crossover, cross-sectional dual momentum, and **Python you write yourself**, sandboxed in a throwaway Docker container, from a terminal that slides over any page |
| **Live news** | Ten broadcast channels, plus headlines from four wire sources |

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
