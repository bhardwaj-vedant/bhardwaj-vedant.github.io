---
title: "Deep Causal Inference for Continuous and Autoregressive Treatments"
collection: portfolio
kind: research
order: 2
excerpt: "Research internship, MBZUAI, Abu Dhabi, May–July 2025 · Advisors: Prof. Jin Tian, Dr. Qiang Huang<br/>Improved a state-of-the-art dose-response estimator (CRNet) with attention-based treatment–covariate interactions and adversarially balanced representations."
---

**Institution:** Mohamed bin Zayed University of Artificial Intelligence (MBZUAI), Abu Dhabi<br/>
**Period:** May 2025 – July 2025 (on-site)<br/>
**Advisors:** Prof. Jin Tian and Dr. Qiang Huang<br/>
**Related code:** [CRNet (fork)](https://github.com/theFulminatedHuman/Contrastive-Balancing-Representation-Network-CRNet)

### Overview
Most causal-inference methods assume a binary treatment: a unit either receives a drug or it does not. Many
real decisions are continuous instead, such as a dose, a price, or a budget, and they often depend on earlier
treatments. The goal is to estimate the whole **dose-response curve**, that is, the expected outcome at every
treatment level. Doing so requires correcting for confounding, because units that receive high doses differ
systematically from those that receive low doses. The project built on CRNet, a contrastive
balancing-representation network, and asked how far attention mechanisms and adversarial balancing could
improve it in the continuous and autoregressive settings.

### Contributions
- Extended **CRNet**, a state-of-the-art model, with attention modules that weight treatment–covariate
  interactions. This reduced the **MISE from 7.55 to 4.3** across 10 treatments and reduced confounding in the
  autoregressive setting.
- Built **adversarially balanced representations** by combining a counterfactual-prediction loss with an
  adversarial treatment-assignment loss, following the ideas of ACFR. The resulting models mitigated bias
  reliably across several continuous-treatment scenarios.
- Outperformed the state of the art on a **simulated benchmark** (10 treatments, 100 covariates), **IHDP**, and
  **News**, using residual GELU encoders, dropout, and attention-based feature fusion.
