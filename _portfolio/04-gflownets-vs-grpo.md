---
title: "GFlowNets vs. GRPO for Mathematical Reasoning in LLMs"
collection: portfolio
kind: research
order: 4
excerpt: "Independent research project, June 2025<br/>A controlled comparison of GFlowNet-based preference optimisation (GDPO) and GRPO on GSM8K math word problems. · [Code](https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks)"
meta: "Independent Research Project · June 2025"
code: "https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks"
overview: |
  Generative Flow Networks (GFlowNets) learn to sample terminal objects with probability proportional to their
  reward. In principle they therefore explore the space of solutions rather than collapsing onto a single mode,
  which is attractive for mathematical reasoning, where a problem typically admits many valid derivations. Group
  Relative Policy Optimisation (GRPO) instead normalises rewards within groups of sampled answers, A = (R −
  μ<sub>g</sub>)/σ<sub>g</sub>, and performs direct on-policy updates. This project compared a GFlowNet-based
  preference method (GDPO) with GRPO on GSM8K, using identical architectures, to test whether the theoretical
  appeal of flow matching survives contact with exact arithmetic.
highlights:
  - "**Accuracy:** GRPO achieved **3× higher accuracy** and more stable training (variance 0.82 vs. 1.21)."
  - "**Why GDPO failed:** it stayed near zero accuracy. Detailed-balance losses spread credit thinly across long trajectories, the learned flows favoured stopping early, and offline preference learning lacked on-policy feedback."
  - "**Scaling:** tests indicate a solve rate above **25%** is reachable with more than 10K pre-training steps and operation-specific reward bonuses."
---

**Type:** Independent research project (June 2025)<br/>
**Code:** [github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks](https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks)

### Overview
Generative Flow Networks (GFlowNets) learn to sample solutions in proportion to their reward, so in principle
they explore many solution paths rather than collapsing onto one. That property seems well suited to
mathematical reasoning, where one question often has several valid derivations. Group Relative Policy
Optimisation (GRPO) is the on-policy method behind many recent reasoning models; it normalises rewards within
groups of sampled answers. This project put the two approaches head-to-head on grade-school math word problems
under matched conditions, in order to see whether the theoretical appeal of GFlowNets carries over in practice.

### Findings
- With identical architectures on **GSM8K**, GRPO achieved **3× higher accuracy** and more stable training
  (variance 0.82 vs. 1.21).
- GDPO's accuracy stayed near 0%. I attribute this to its offline preference learning, which lacks the on-policy
  feedback that GRPO exploits.
- Scaling tests suggest a solve rate above **25%** is reachable with over 10K pre-training steps and
  operation-specific reward bonuses.

### Why the GFlowNet approach struggled
- **Credit assignment.** Detailed-balance losses spread the reward signal thinly across long reasoning
  trajectories, and exact arithmetic needs precise credit on individual steps.
- **Early termination.** The learned flows favoured stopping early over completing a solution.
- **Offline preference learning.** Without the on-policy feedback that GRPO receives, the GFlowNet policy
  never found enough correct trajectories to learn from.
