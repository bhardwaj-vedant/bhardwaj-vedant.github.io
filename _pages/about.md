---
permalink: /
title: "About Me"
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

{% include base_path %}

I am an undergraduate student at the **Indian Institute of Technology Bombay**, where I am also pursuing a
**Minor in Machine Learning and Artificial Intelligence** offered by the Centre for Machine Intelligence and
Data Science (C-MInDS). I am interested in **machine learning research**, and I hope to pursue it
further as a career.

My work sits where theory meets practice in deep learning. I want to understand *why* modern models
generalise, and to turn that understanding into rules practitioners can use. Most recently, at
**National Taiwan University**, I used random matrix theory to show that LoRA rank does not act as a
model-size axis for double descent, and I derived a principled rank-selection rule for parameter-efficient
fine-tuning. Before that, at **Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)**, I worked on
causal inference with continuous and autoregressive treatments, where I improved a state-of-the-art
dose-response estimator.

Research Interests
======
- **Theory of deep learning:** generalisation, double descent, and random matrix theory
- **High-dimensional probability and statistics:** concentration of measure, spectral methods, and their use in learning theory
- **Parameter-efficient fine-tuning and LLM post-training**
- **Causal inference:** treatment-effect and dose-response estimation with deep models
- **Reinforcement learning and sequential decision-making**
- **Interpretability and safety of language models**
- **Generative modelling**

I have studied Roman Vershynin's *High-Dimensional Probability: An Introduction with Applications in Data
Science*. It provides much of the mathematical toolkit I rely on in my research, from concentration inequalities
to the spectral behaviour of random matrices. My coursework in High-Dimensional Statistics, Convex
Optimisation, Probability and Random Processes, and Safety-Critical Methods in Machine Learning supports the
same direction.

Research Overview
======

### Does LoRA Rank Play the Role of Model Size?
*Research internship, National Taiwan University · May – July 2026 · Advisors: Prof. Yen-Huan Li, Dr. Ying-Ting Lin*

Double descent is usually studied by varying width, depth, or training time. LoRA rank looks like a natural
fourth axis, and choosing a rank is a decision every practitioner faces. I asked whether rank really behaves
like model size. For a single linear adapter I proved that it cannot: a rank-*r* adapter never has more than
*m·d* degrees of freedom, so the risk curve levels off instead of peaking, and rank acts as a spectral
regulariser rather than as a capacity knob. I then tested the loopholes the theorem leaves open (stacked
adapters, non-linearity, gradient-descent training, label noise) in a two-layer transformer. Test loss still
rose at all 12 rank steps. The peak does exist, but along the data-to-dimension ratio *n/d* rather than
along rank. Building on this, I derived a rule from the BBP phase transition of spiked random matrices: keep
only the adapter directions whose signal clears the noise bulk. Monte Carlo simulations confirm it to within
1.5%, and it reduces the error in predicted risk from 40% to 3%. In practice, on small noisy datasets the
best rank was 1, and the common default of 64 cost 1.47 nats per token.
[Details]({{ base_path }}/portfolio/01-lora-double-descent/) ·
[Code](https://github.com/theFulminatedHuman/LORA-DoubleDescent)

### Deep Causal Inference for Continuous and Autoregressive Treatments
*Research internship, MBZUAI, Abu Dhabi · May – July 2025 · Advisors: Prof. Jin Tian, Dr. Qiang Huang*

Many real treatments are continuous (a drug dose, an advertising budget) and depend on earlier treatments,
which makes confounding harder to remove than in the binary case. I worked on estimating dose-response curves
in this setting. I extended CRNet, a state-of-the-art estimator, with attention modules that learn how
treatment and covariates interact, and I added adversarial balancing inspired by ACFR so that the learned
representations carry less information about which treatment a unit received. The model reduced the mean
integrated squared error (MISE) from 7.55 to 4.3 across 10 treatments, and it outperformed the baseline on a
simulated benchmark, IHDP, and News.
[Details]({{ base_path }}/portfolio/02-causal-dose-response/)

### Mechanistic Interpretability of Deceptive Behaviour in LLMs
*Course research project (DS606), IIT Bombay · 2026 · Advisor: Prof. Arjun Bhagoji*

Can we find where a language model represents deception, and switch it off without harming the rest of the
model? Linear probes on GPT-2-XL and Llama-3.2-3B decoded deceptive context at every layer (AUROC above
0.93). Two PCA-based methods for extracting a direction compressed 87–93% of the relevant variance into a
single component. Projecting that direction out at the best layer reduced KL divergence by about 13.7%. I
checked the side effects on MMLU, HellaSwag, and BERTScore, comparing against representation-engineering and
random-ablation baselines.
[Details]({{ base_path }}/portfolio/03-mech-interp-deception/)

### GFlowNets vs. GRPO for Mathematical Reasoning
*Independent research project · June 2025*

GFlowNets are designed to sample diverse solutions, which in principle suits reasoning problems that have
many valid paths to an answer. I compared a GFlowNet-based preference method (GDPO) with GRPO on GSM8K using
identical architectures. GRPO reached three times the accuracy and trained more stably. The GFlowNet approach
stayed near zero accuracy: it struggled with credit assignment over long trajectories and learned to stop
early rather than finish solutions.
[Details]({{ base_path }}/portfolio/04-gflownets-vs-grpo/) ·
[Code](https://github.com/theFulminatedHuman/GFlowNets-vs-GRPO-in-LLM-Math-Tasks)

Selected Projects
======

### High-Performance Poker Equity Engine
*C++20 · August 2026*

This is a heads-up Texas Hold'em equity calculator that computes win, tie, and loss probabilities either
exactly, by enumerating every board, or by multithreaded Monte Carlo sampling with confidence intervals. A
minimal perfect hash over card bitboards evaluates a seven-card hand in 8.9 ns, 346 times faster than the
starting implementation, and the engine enumerates all 1.7 million pre-flop boards in about 13 ms on four
cores. It has 149 tests, and it is served through a REST API with a web front end.
[Details]({{ base_path }}/portfolio/11-poker-equity-engine/) ·
[Code](https://github.com/theFulminatedHuman/Poker-Engine)

### Intraday Equity Trading Engine
*C++20 · September 2026*

This is a dependency-free intraday trading engine for Indian equities (NSE/BSE). Its design principle is that
backtesting, paper trading, and live trading all run the same strategy code through the same risk checks, so
a strategy that has been tested behaves the same way in production. It has 121 tests.
[Details]({{ base_path }}/portfolio/12-cpp-trading-engine/) ·
[Code](https://github.com/theFulminatedHuman/cpp-trading-engine)

### Reinforcement Learning and Decision Making
*Course project (CS747), IIT Bombay · August – November 2025*

I solved Markov decision processes exactly with Howard's policy iteration and linear programming. I also built
a game-playing agent for 5×4 Microchess that uses depth-limited Minimax search with a hand-designed evaluation
of material, mobility, and king safety.
[Details]({{ base_path }}/portfolio/13-rl-decision-making/)

### Neural Reinforcement Learning for Portfolio Optimisation
*Finance Club, IIT Bombay · July 2025*

I implemented six deep RL algorithms (DQN, PPO, A2C, DDPG, TD3, and SAC) from scratch and applied DDPG to
continuous portfolio rebalancing on NIFTY50 data, comparing it with ARIMA, Prophet, and LSTM forecasting
baselines.
[Details]({{ base_path }}/portfolio/15-deep-rl-portfolio/) ·
[Code](https://github.com/theFulminatedHuman/RL-in-Stock-Trading-Optimization)

The complete lists are on the [Research]({{ base_path }}/research/) and [Projects]({{ base_path }}/projects/)
pages.

Education
======
**Indian Institute of Technology Bombay**, Mumbai, India
- Undergraduate degree, with a Minor in Machine Learning and Artificial Intelligence (C-MInDS)
- Completed 168 academic credits in the first three years, with 14 AA/AB grades
- Selected coursework: High-Dimensional Statistics, Convex Optimisation, Introduction to Probability and Random
  Processes, Safety-Critical Methods in ML, Foundations of Intelligent Agents, Reinforcement Learning (CS747),
  Game Theory, Computer Networks, Programming for Data Science (DS203)

Beyond Research
======
Outside research I enjoy astronomy and astrophotography, competitive programming, strategy games, and
reverse engineering. More on the [Hobbies]({{ base_path }}/hobbies/) page.
