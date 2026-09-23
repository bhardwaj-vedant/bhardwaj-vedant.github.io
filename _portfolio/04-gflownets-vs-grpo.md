---
title: "GFlowNets vs. GRPO for Mathematical Reasoning in LLMs"
collection: portfolio
kind: research
order: 4
excerpt: "Independent research project, June 2025<br/>A controlled comparison of GFlowNet-based preference optimisation (GDPO) and GRPO on GSM8K math word problems. · [Code](https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks)"
---

**Type:** Independent research project (June 2025)<br/>
**Code:** [github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks](https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks)

### Overview
This project asked how GFlowNet-based preference optimisation (GDPO) compares with GRPO, the prevailing
state-of-the-art method, for training LLMs on mathematical reasoning.

### Findings
- With identical architectures on **GSM8K**, GRPO achieved **3× higher accuracy** and more stable training
  (variance 0.82 vs. 1.21).
- GDPO's accuracy stayed near 0%. I attribute this to its offline preference learning, which lacks the on-policy
  feedback that GRPO exploits.
- Scaling tests suggest a solve rate above **25%** is reachable with over 10K pre-training steps and
  operation-specific reward bonuses.
