---
title: "High-Performance Poker Equity Engine"
collection: portfolio
kind: project
order: 11
excerpt: "Independent project, August 2026 · C++20<br/>A Texas Hold'em equity solver that combines exact enumeration with multithreaded Monte Carlo; hand evaluation is 346× faster than the baseline. · [Code](https://github.com/theFulminatedHuman/Poker-Engine)"
meta: "Independent Project · C++20 · August 2026"
code: "https://github.com/theFulminatedHuman/Poker-Engine"
overview: |
  This is a heads-up Texas Hold'em equity engine. Given the hero's hole cards, the villain's cards or a weighted
  range, and any community cards, it computes the equity E<sub>H</sub> = P(win) + ½·P(tie). It can do this
  exactly, by enumerating every remaining board, or by multithreaded Monte Carlo sampling that reports standard
  errors and confidence intervals and keeps sampling until a requested tolerance is met. The project is an
  exercise in performance engineering held to a correctness oracle: a minimal perfect hash over bitboards for
  hand evaluation, cache-aware multithreading, and an exhaustive test suite that checks the sampler against
  exact enumeration. The engine is available through a command-line tool, a REST API, and a web front end.
highlights:
  - "**Hand evaluation:** seven-card evaluation went from **3080 ns to 8.9 ns (346×)**, using a minimal perfect hash over 49,205 seven-card rank multisets on 52-bit bitboards."
  - "**Throughput:** exact enumeration of all **1,712,304** pre-flop boards takes about 13 ms on 4 cores, and Monte Carlo runs at **49.5M trials/s**."
  - "**Scaling and correctness:** removing false sharing in per-worker RNG and deck state lifted 4-thread scaling from **1.24× to 2.87×**. 149 tests confirm the expected O(1/√N) convergence."
---

**Type:** Independent project (August 2026)<br/>
**Code:** [github.com/theFulminatedHuman/Poker-Engine](https://github.com/theFulminatedHuman/Poker-Engine)

### Overview
Given each player's hole cards (or a weighted range of possible hands for the opponent) and any community
cards already dealt, the engine computes each player's **equity**: the probability of winning plus half the
probability of a tie. It offers two modes. **Exact mode** enumerates every possible remaining board. **Monte
Carlo mode** samples boards across several threads and reports a standard error and confidence interval, and
it can keep sampling until a requested precision is reached. The engine is available from the command line,
as a REST API, and through a web front end.

| | |
|---|---|
| 7-card hand evaluation | 8.9 ns from a bare mask (12.3 ns on the hot path) |
| Exact pre-flop enumeration | all 1,712,304 boards in about 13 ms on 4 cores |
| Monte Carlo throughput | 49.5 M trials/s on 4 cores |
| Parallel scaling | 3.9× (exact) and 2.9× (Monte Carlo) on 4 cores |
| Correctness | 149 tests; the solver matches an independent brute-force implementation exactly |

### Engineering highlights

- Designed a Texas Hold'em equity engine in **C++20**. It hashes all **49,205** seven-card rank multisets into
  a minimal perfect-hash table over 52-bit bitboards with incremental accumulation, cutting evaluation time from
  **3080 ns to 8.9 ns (346×)**.
- Found and removed **false sharing** in per-worker RNG and deck state. Cache-line alignment improved
  4-thread scaling from **1.24× to 2.87×**.
- Checked exact enumeration of 1.7M boards against sampling across **149 tests**, confirming the expected
  O(1/√N) Monte Carlo error at **49.5M trials/s**.
