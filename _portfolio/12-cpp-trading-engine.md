---
title: "Intraday Equity Trading Engine in C++"
collection: portfolio
kind: project
order: 12
excerpt: "Independent project, September 2026 · C++20<br/>A dependency-free intraday trading engine for NSE/BSE equities. The same strategy code and risk gate drive backtesting, paper trading, and live trading. · [Code](https://github.com/theFulminatedHuman/cpp-trading-engine)"
---

**Type:** Independent project (September 2026)<br/>
**Code:** [github.com/theFulminatedHuman/cpp-trading-engine](https://github.com/theFulminatedHuman/cpp-trading-engine)

### Overview
Trading systems often fail at the boundary between research and production, when a strategy that looked
good in a backtest behaves differently live. This engine addresses that problem by construction: the same
strategy code and the same risk gate run in every mode, and only the data source and the order sink change. It
is written in standard C++20 with no third-party libraries, and it includes tools for fetching real market
history.

### Features

- An intraday trading engine for NSE/BSE equities, written in **C++20** with **no external dependencies**.
- A single architecture supports **backtesting**, **paper trading**, and **live trading**. The same strategy code
  and the same risk gate apply in every mode, so behaviour in simulation matches behaviour in production.
- Includes a simulated market feed, tick-replay backtesting, an interactive operator console (positions, orders,
  kill switch), and a test suite of **121 tests with 583 checks**.
