---
title: "Deep Generative Models: GANs, VAEs, and Flow Matching"
collection: portfolio
kind: project
order: 16
excerpt: "Seasons of Code, Web and Coding Club, IIT Bombay, June–August 2024<br/>Implemented DCGANs, VAEs, and flow-matching models from scratch for image generation on CelebA. · [Code](https://github.com/theFulminatedHuman/DGMs---SOC)"
meta: "Seasons of Code · Web and Coding Club, IIT Bombay · June – August 2024"
code: "https://github.com/theFulminatedHuman/DGMs---SOC"
overview: |
  This project studied the three main families of deep generative models (adversarial, variational, and
  flow-based), implementing each from scratch and training on the CelebA face dataset. The emphasis was on the
  failure modes that separate the families in practice: mode collapse and training instability in GANs, the
  trade-off between reconstruction and regularisation in VAEs, and the conditioning of flow-matching objectives.
highlights:
  - "**GAN:** a **DCGAN** built from scratch, with depth, batch size, and learning rate tuned for sharper, less noisy samples."
  - "**Flow matching:** loss design, learning-rate schedules, and **Optuna** tuning gave stable convergence and less mode collapse."
  - "**VAEs:** upsampling and downsampling **VAEs** on 50K CelebA images, with gradient explosions resolved."
---

**Organisation:** Seasons of Code, Web and Coding Club, IIT Bombay (June – August 2024)<br/>
**Code:** [DGMs---SOC](https://github.com/theFulminatedHuman/DGMs---SOC) · [DCGAN on CelebA](https://github.com/theFulminatedHuman/DCGAN-implementation-on-CelebA-dataset)

### Overview
Seasons of Code is a mentored summer programme at IIT Bombay. This project worked through the three main
families of deep generative models: generative adversarial networks, variational autoencoders, and
flow-matching and diffusion models. Each was implemented from scratch and trained on the CelebA face dataset
for image generation.

### What I did

- Implemented a **Deep Convolutional GAN** from scratch on CelebA and tuned depth, batch size, and learning
  rate to produce sharper, less noisy samples.
- Improved **flow-matching** training with loss-function design, learning-rate schedules, and automated
  hyperparameter search with Optuna. This gave stable convergence and less mode collapse across the GAN and VAE
  variants.
- Built upsampling and downsampling **VAEs** on 50K CelebA images. Fixing gradient explosions and refining the
  latent representation improved reconstruction quality and sped up GAN convergence.
