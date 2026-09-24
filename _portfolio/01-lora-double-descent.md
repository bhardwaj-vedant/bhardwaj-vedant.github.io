---
title: "Does LoRA Rank Play the Role of Model Size? A Random-Matrix Study of Double Descent"
collection: portfolio
kind: research
order: 1
excerpt: "Research internship, National Taiwan University, May–July 2026 · Advisors: Prof. Yen-Huan Li, Dr. Ying-Ting Lin<br/>A random-matrix-theory analysis of rank-wise double descent in parameter-efficient fine-tuning, with a closed-form rule for choosing the LoRA rank. · [Code](https://github.com/theFulminatedHuman/LORA-DoubleDescent)"
meta: "Research Internship · National Taiwan University · May – July 2026 · Advisors: Prof. Yen-Huan Li, Dr. Ying-Ting Lin"
code: "https://github.com/theFulminatedHuman/LORA-DoubleDescent"
overview: |
  Double descent is classically indexed by width, depth or training time. LoRA rank is the natural candidate for
  a fourth axis, and choosing it is a decision every practitioner faces. This project uses random matrix theory
  to ask whether rank genuinely plays the role of model size. For a single linear adapter fitted to its global
  optimum, a no-go theorem shows that it cannot. The rank-*r* manifold has at most *m·d* degrees of freedom, so
  interpolation is reachable only when *n ≤ d*, and in exactly that regime the fit is already pinned down by the
  row space of the design. The risk therefore saturates rather than peaking. Rank instead acts as a spectral
  regulariser, equivalent to a ridge penalty of computable strength λ<sub>eff</sub>(r). Modelling the
  fine-tuning update as a spike in a rectangular random matrix, the Baik–Ben Arous–Péché (BBP) phase transition
  yields a closed-form rule: the optimal rank is the number of adapter directions whose signal strength exceeds
  the detection threshold θ<sub>c</sub> = σ(md)<sup>1/4</sup>/√(n − d − 1).
highlights:
  - "**A no-go theorem:** LoRA rank provably does not index double descent for a linear adapter. A gradient-trained two-layer transformer with 25% label noise confirms that test loss rises monotonically across all 12 ranks."
  - "**A BBP rank-selection rule:** Monte Carlo simulations confirm it to within **1.5%**, and it reduces the error in predicted risk from **40% to 3%**."
  - "**Where the peak really is:** the peak lies on the *n/d* axis. At fixed rank the optimum collapses to zero at *n = d*. On small, noisy datasets the optimal rank is **1**, and the common default r = 64 costs **1.47 nats/token**."
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

### Rank as a regulariser, not a capacity knob
For a single linear adapter the rank-*r* matrices can only interpolate the data when *n ≤ d*. In exactly that
regime the fit is already pinned down by the row space of the design matrix, so increasing the rank cannot
produce the classical rise and fall. Instead, rank behaves like a **spectral regulariser**: it is equivalent to
a ridge penalty with a computable effective strength λ<sub>eff</sub>(r).

### A rank-selection rule from random matrix theory
The fine-tuning update can be modelled as a low-rank "spike" hidden in a noisy random matrix. The
Baik–Ben Arous–Péché (BBP) phase transition says which spike directions can be detected at all, and the optimal
rank is simply the number of adapter directions whose strength exceeds that threshold. The same analysis gives
a data-scaling law: halving a direction's strength requires four times as much data to recover it.

### Validation
- The BBP threshold, outlier locations, and overlap formulas match Monte Carlo simulations to within 1.5%.
- The corrected noise scale σ/√(n−d−1) matches to within 1%. The naive σ/√n is off by a factor of 2 at n = 2d.
- The closed-form risk curve tracks the exact solver, with median relative error between 3.4% and 11.3%, and it
  identifies the optimal rank exactly in every configuration tested.
- Across 300 random configurations, every measured risk curve was U-shaped and rose to a plateau, with no
  interior peak.

### Where the theory stops
When *n* is swept with the rank held fixed, the measured optimal rank collapses to **zero at n = d**, meaning
no adapter at all is best. This is a genuine double-descent peak, but it lies on the *n/d* axis, not on the rank
axis. Rank and width are not interchangeable. The study reports where the theory loses accuracy and explains
why; for example, at n = 2d the noise spectrum departs from the Marchenko–Pastur law.
