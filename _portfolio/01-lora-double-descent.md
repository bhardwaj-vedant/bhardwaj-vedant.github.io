---
title: "Does LoRA Rank Play the Role of Model Size? A Random-Matrix Study of Double Descent"
collection: portfolio
kind: research
order: 1
excerpt: "Research internship, National Taiwan University, May–July 2026 · Advisors: Prof. Yen-Huan Li, Dr. Ying-Ting Lin<br/>A random-matrix-theory analysis of rank-wise double descent in parameter-efficient fine-tuning, with a closed-form rule for choosing the LoRA rank. · [Code](https://github.com/theFulminatedHuman/LORA-DoubleDescent)"
---

**Institution:** National Taiwan University, Taipei<br/>
**Period:** May 2026 – July 2026<br/>
**Advisors:** Prof. Yen-Huan Li and Dr. Ying-Ting Lin<br/>
**Code:** [github.com/theFulminatedHuman/LORA-DoubleDescent](https://github.com/theFulminatedHuman/LORA-DoubleDescent)

### Motivation
Classical double descent is studied along width, depth, or training time. LoRA rank is a natural fourth axis,
and "which rank should I use?" is a question every practitioner has to answer. This project asks whether rank
behaves like model size and, if it does not, what rank a practitioner should actually choose.

### Contributions
- **A no-go theorem.** For a single linear adapter fitted to its global optimum, a rank-*r* adapter has at most
  *m·d* degrees of freedom. As a result, the risk curve saturates rather than peaking, and LoRA rank provably
  does **not** act as a model-size axis for double descent.
- **Empirical confirmation.** In a gradient-trained two-layer transformer with LoRA on four weight matrices and
  25% label noise, test loss rose at **all 12 rank steps**. There was no interior peak, even though training loss
  crossed the Bayes error near the predicted interpolation threshold.
- **A rank-selection rule.** Using random matrix theory, I derived a rule that keeps only the adapter directions
  that clear the Baik–Ben Arous–Péché (BBP) detection threshold. Monte Carlo simulations confirm it to within
  **1.5%**, and it reduces the error in predicted risk from **40% to 3%**.
- **Practical guidance.** On noisy fine-tuning data the optimal rank was **r = 1**. The common default of
  r = 64 costs **1.47 nats/token** of test loss.
