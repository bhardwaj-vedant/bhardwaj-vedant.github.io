---
layout: archive
title: "Experience"
permalink: /experience/
author_profile: true
---

{% include base_path %}

Research Experience
======
**Deep Learning Research Intern**, National Taiwan University, Taipei *(May 2026 – July 2026)*<br/>
Random matrix theory for parameter-efficient fine-tuning: rank-wise double descent in LoRA.
[Details]({{ base_path }}/portfolio/01-lora-double-descent/)

**Machine Learning Research Intern**, MBZUAI, Abu Dhabi *(May 2025 – July 2025)*<br/>
Deep causal inference and dose-response estimation for continuous and autoregressive treatments.
[Details]({{ base_path }}/portfolio/02-causal-dose-response/)

Professional Experience
======
**Founder & Lead Researcher**, Alqeora Labs *(independent venture, 2026 – present)*<br/>
*Virtual perturbation screens: predicting how a population of human cells responds to a drug, a gene edit, or a
combination, before the experiment is run.*
[Details]({{ base_path }}/portfolio/05-alqeora-labs/)
- Building an **unbalanced conditional flow-matching** model on the **Fisher–Rao sphere** of single-cell
  expression profiles. It learns a joint velocity and growth field from unpaired control and perturbed
  populations, using within-plate entropic unbalanced optimal-transport couplings and a mass-weighted loss
  (PyTorch, POT), to predict responses and viability for unseen compounds and genes.
- Introduced a transcriptome-wide generalisation of **Bliss synergy** built from additive tangent and growth
  fields, and an ensemble-disagreement **experiment-ranking** (active-learning) method that chooses which wet-lab
  experiments would most reduce model uncertainty.
- Designed a pre-registered, distribution-level validation protocol on public Perturb-seq and sci-Plex data,
  scored on held-out perturbations against linear, nearest-neighbour, GEARS, CPA and CellOT baselines.

**Machine Learning Engineer**, Unmesh Mashruwala Innovation Cell (UMIC), IIT Bombay *(September 2024 – April 2025)*<br/>
*UMIC is a student technical team of 50+ members that builds autonomous aerial and ground robots.*
- Implemented a **ResNet** from scratch in PyTorch for ImageNette and tuned depth, learning rate, and
  regularisation with **Optuna** to improve accuracy and generalisation.
  ([Code](https://github.com/theFulminatedHuman/Resnet-Paper-Implementation-on-Imagenet))
- Monitored loss curves, weight gradients, and hyperparameter sweeps in real time with **TensorBoard**.
- Set up a multi-node GPU environment (NVIDIA A5000, Titan XP) for training and inference on ImageNet.

**Task Mapping with LLMs**, Bharat Forge Problem Statement, Inter IIT Tech Meet *(November 2024 – December 2024)*<br/>
*The competition asked teams to design scalable multi-agent swarms for dynamic path planning in GPS-denied environments.*
- Designed a command-parsing framework that uses regular expressions to extract locations and dynamic objects.
- Used LLMs through the Hugging Face APIs to classify commands as *detect* or *move* tasks.
- Resolved ambiguous tasks with contextual keyword analysis and dynamic rule-based assignment.

Leadership and Extracurricular Activities
======
**Volunteer**, Krittika – The Astronomy Club of IIT Bombay *(May 2024 – October 2024)*
- Helped run Krittika's Summer Project (KSP) and organised an astrophotography exhibition for National Space Day.
- Created a Learners' Space course on **spectroscopy and photometry** for astrophotography from scratch.

Technical Skills
======
- **Programming:** Python, C++, C, CUDA, Assembly
- **Machine learning:** PyTorch, Keras, scikit-learn, Optuna, TensorBoard, statsmodels, NumPy, pandas
- **Tools:** Git, Docker, tmux, MATLAB/Simulink, ROS, Gazebo, Wireshark, Nmap

Achievements
======
- Completed 168 academic credits with 14 AA/AB grades in the first three years of undergraduate study.
- Solved more than 300 algorithmic problems on LeetCode and CSES, focusing on graphs, trees, and dynamic programming.
