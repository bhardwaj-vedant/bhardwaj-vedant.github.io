---
title: "High-Performance Poker Equity Engine"
collection: portfolio
kind: project
order: 11
excerpt: "Independent project, August 2026 · C++20<br/>A Texas Hold'em equity solver that combines exact enumeration with multithreaded Monte Carlo; hand evaluation is 346× faster than the baseline. · [Code](https://github.com/theFulminatedHuman/Poker-Engine)"
---

**Type:** Independent project (August 2026)<br/>
**Code:** [github.com/theFulminatedHuman/Poker-Engine](https://github.com/theFulminatedHuman/Poker-Engine)

- Designed a Texas Hold'em equity engine in **C++20**. It hashes all **49,205** seven-card rank multisets into
  a minimal perfect-hash table over 52-bit bitboards with incremental accumulation, cutting evaluation time from
  **3080 ns to 8.9 ns (346×)**.
- Found and removed **false sharing** in per-worker RNG and deck state. Cache-line alignment improved
  4-thread scaling from **1.24× to 2.87×**.
- Checked exact enumeration of 1.7M boards against sampling across **149 tests**, confirming the expected
  O(1/√N) Monte Carlo error at **49.5M trials/s**.
