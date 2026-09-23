---
title: "Mechanistic Interpretability of Deceptive Behaviour in LLMs"
collection: portfolio
kind: research
order: 3
excerpt: "Course research project (DS606), IIT Bombay, 2026 · Advisor: Prof. Arjun Bhagoji<br/>Located and suppressed deception-related representations in open-source LLMs using layer-wise probes and activation steering."
---

**Course:** DS606, IIT Bombay (2026)<br/>
**Advisor:** Prof. Arjun Bhagoji

### Overview
This project probed and suppressed deceptive behaviour in open-source language models by analysing their
internal activations.

### Contributions
- Located context-sensitivity to deception in **GPT-2-XL** and **Llama-3.2-3B** using layer-wise linear probes.
  The probes reached a 5-fold cross-validated **AUROC above 0.93 at every layer** of both architectures.
- Developed two PCA-based direction-extraction methods: population-level mean difference and per-pair
  subtraction. Both concentrated **87–93% of the variance** onto a single principal component, isolating the
  models' deception representations.
- Designed forward-hook projections at each model's best layer, reducing KL divergence by about **13.7%**.
- Compared these interventions with RepE-style engineering and random ablations on **MMLU**, **HellaSwag**,
  and **BERTScore** to measure side effects on general capability.
