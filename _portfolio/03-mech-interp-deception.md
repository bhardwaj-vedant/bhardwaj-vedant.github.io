---
title: "Mechanistic Interpretability of Deceptive Behaviour in LLMs"
collection: portfolio
kind: research
order: 3
excerpt: "Course research project (DS606), IIT Bombay, 2026 · Advisor: Prof. Arjun Bhagoji<br/>Located and suppressed deception-related representations in open-source LLMs using layer-wise probes and activation steering."
meta: "Course Research Project (DS606) · IIT Bombay · 2026 · Advisor: Prof. Arjun Bhagoji"
overview: |
  If a language model can behave deceptively, we would like to know where in the network that behaviour is
  represented and whether it can be removed without collateral damage. This project adopts the linear
  representation hypothesis and treats deception as a direction in activation space. Layer-wise linear probes
  first test whether that direction can be decoded. PCA-based extraction then isolates it, and forward-hook
  projections remove it during inference. Each intervention is evaluated for its effect on deceptive behaviour
  and for its side effects on general capability.
highlights:
  - "**Probing:** probes on **GPT-2-XL** and **Llama-3.2-3B** reach a 5-fold cross-validated AUROC **above 0.93 at every layer**."
  - "**Direction extraction:** two methods, population-level mean difference and per-pair subtraction, concentrate **87–93%** of the variance onto a single principal component."
  - "**Intervention:** projecting out the direction at the best layer reduces KL divergence by about **13.7%**. It was compared against representation engineering (RepEng) and random ablations on MMLU, HellaSwag, and BERTScore."
---

**Course:** DS606, IIT Bombay (2026)<br/>
**Advisor:** Prof. Arjun Bhagoji

### Overview
If a model can behave deceptively, we would like to know *where* in the network that behaviour is represented
and whether it can be removed surgically. This project treats deception as a direction in activation space. It
first tests, layer by layer, whether that direction can be decoded at all. It then extracts the direction with
PCA and projects it out during the forward pass. Finally, it measures both how much the deceptive behaviour
changes and how much general capability is lost as a side effect.

### Contributions
- Located context-sensitivity to deception in **GPT-2-XL** and **Llama-3.2-3B** using layer-wise linear probes.
  The probes reached a 5-fold cross-validated **AUROC above 0.93 at every layer** of both architectures.
- Developed two PCA-based direction-extraction methods: population-level mean difference and per-pair
  subtraction. Both concentrated **87–93% of the variance** onto a single principal component, isolating the
  models' deception representations.
- Designed forward-hook projections at each model's best layer, reducing KL divergence by about **13.7%**.
- Compared these interventions with RepE-style engineering and random ablations on **MMLU**, **HellaSwag**,
  and **BERTScore** to measure side effects on general capability.
