---
title: "Alqeora Labs: Unbalanced Riemannian Flow Matching for Virtual Perturbation Screens"
collection: portfolio
order: 5
excerpt: "Founder's Office Intern, Alqeora Labs, December 2025 – January 2026 · Predicting how a population of human cells responds to a drug, a gene edit, or a combination, before the experiment is run."
meta: "Founder's Office Internship · Alqeora Labs · December 2025 – January 2026"
---

**Role:** Founder's Office Intern, working directly with the co-founders<br/>
**Period:** December 2025 – January 2026

## About Alqeora Labs

Alqeora Labs predicts how a population of human cells will respond to a drug, a gene edit, or a combination of
them, before the experiment is run. Given a cell line and a perturbation, it predicts the full distribution of
resulting single-cell expression states and the population's viability, scores drug and gene combinations for
synergy, and ranks untested experiments by how much running them would teach the model.

The engine is an **unbalanced, conditional, Riemannian flow-matching model**. It transports the control-cell
population to the perturbed population on the Fisher–Rao sphere of gene-expression profiles, while a learned
growth field accounts for cells that die or proliferate. Each piece of the mathematics answers a fact about the
biology: sequencing destroys cells, so only unpaired populations are observed (optimal transport); perturbations
kill and multiply cells (unbalanced transport); counts are multinomial (Fisher–Rao geometry); identical cells
respond stochastically (entropic regularization); and the perturbation space is combinatorial (conditioning on
perturbation embeddings).

## The mathematics

### Problem

Fix a cellular context $$c$$ and a perturbation $$\pi$$. Control cells are drawn from a measure $$\nu_0^c$$ and
perturbed cells from an *unnormalized* measure $$\nu_1^{c,\pi}$$, whose total mass $$m(c,\pi)$$ is the viability.
The goal is the map

$$
(c,\ \pi) \;\longmapsto\; \nu_1^{c,\pi} = m(c,\pi)\,\mu_1^{c,\pi}, \qquad \pi \in \Pi_{\text{unseen}} \cup \{\pi_1 \oplus \pi_2\}
$$

Because cells die and divide, mass is not conserved and the population obeys a continuity equation with a source
term ($$g_t > 0$$ is proliferation, $$g_t < 0$$ is death):

$$
\partial_t \rho_t + \operatorname{div}(\rho_t v_t) = g_t\,\rho_t
$$

### Fisher–Rao geometry

A cell's normalized counts are a point $$p$$ on the simplex $$\Delta^{G-1}$$. The Fisher information of the
multinomial gives the Fisher–Rao metric, the geometry in which measurement noise is isotropic:

$$
g^{\mathrm{FR}}_p(u, v) = \sum_{i=1}^{G} \frac{u_i v_i}{p_i}, \qquad u, v \in T_p\Delta = \{u : \textstyle\sum_i u_i = 0\}
$$

The square-root map is an isometry onto the positive orthant of the sphere of radius 2:

$$
\phi : \Delta^{G-1} \to S^{G-1}_{+}(2), \qquad \phi(p) = 2\sqrt{p}, \qquad d_{\mathrm{FR}}(p, q) = 2\arccos\Big(\sum_{i=1}^{G}\sqrt{p_i q_i}\Big)
$$

With $$R = 2$$ and $$\theta = \arccos(\langle s_0, s_1\rangle / R^2)$$, the exponential map, geodesic and
logarithm are closed-form:

$$
\begin{aligned}
\operatorname{Exp}_s(u) &= \cos\!\big(\tfrac{\|u\|}{R}\big)\, s + R \sin\!\big(\tfrac{\|u\|}{R}\big) \frac{u}{\|u\|}, \qquad u \in T_sS = \{u : \langle u, s\rangle = 0\},\\
\gamma(t) &= \frac{\sin((1-t)\theta)\, s_0 + \sin(t\theta)\, s_1}{\sin\theta}, \qquad \operatorname{Log}_{s_0}(s_1) = R\,\theta \,\frac{s_1 - \cos\theta\, s_0}{\|s_1 - \cos\theta\, s_0\|}
\end{aligned}
$$

In the positive orthant $$\theta \le \pi/2$$, far from the cut locus at $$\theta = \pi$$, so geodesics between
cells are unique and stay in the orthant. The fold $$\psi(s) = (s \odot s)/R^2$$, renormalized, maps the whole
sphere back onto the simplex. Control and perturbed populations are empirical measures on the sphere:

$$
\nu_0^{c} = \frac{1}{n_0}\sum_{i=1}^{n_0} \delta_{s_i}, \qquad \nu_1^{c,\pi} = \frac{m(c,\pi)}{n_1}\sum_{j=1}^{n_1} \delta_{s_j}, \qquad m(c,\pi) = \frac{\nu_1^{c,\pi}(S)}{\nu_0^{c}(S)}
$$

### Unbalanced dynamics

A cell is a particle $$X_t$$ carrying a mass $$W_t$$, moved by a tangent velocity field and reweighted by a
growth field:

$$
\frac{dX_t}{dt} = v_t\big(X_t \mid c, \pi\big) \in T_{X_t}S, \qquad \frac{d\log W_t}{dt} = g_t\big(X_t \mid c, \pi\big), \qquad \rho_t = \mathbb{E}\big[\,W_t\,\delta_{X_t}\big]
$$

The measure $$\rho_t$$ then solves the unbalanced continuity equation, in weak form on the sphere:

$$
\partial_t \rho_t + \operatorname{div}_g(\rho_t v_t) = g_t\,\rho_t
\quad\Longleftrightarrow\quad
\frac{d}{dt}\int_S \varphi\, d\rho_t = \int_S \Big( \langle \nabla_g \varphi, v_t \rangle_g + g_t\, \varphi \Big)\, d\rho_t \quad \forall \varphi \in C^\infty(S)
$$

The action-minimizing $$(v, g)$$ define the Wasserstein–Fisher–Rao distance, where the length scale $$\delta$$
separates "cells changed state" from "one subpopulation died while another expanded":

$$
\mathrm{WFR}_\delta^2(\nu_0, \nu_1) = \inf_{(\rho, v, g)} \int_0^1\!\!\int_S \Big( \|v_t\|_g^2 + \frac{\delta^2}{4}\, g_t^2 \Big)\, d\rho_t\, dt
$$

### Conditional paths and the weighted marginalization theorem

Given an unbalanced coupling $$\pi_{ij}$$ between control cell $$s_i$$ (mass $$a_i$$) and perturbed cell
$$s_j$$, let $$r_i = (\sum_j \pi_{ij})/a_i$$. Each pair moves along its geodesic and grows exponentially:

$$
X_t = \gamma_{s_i \to s_j}(t), \quad u_t = \dot\gamma_{s_i \to s_j}(t) = \frac{\theta_{ij}\big(\cos(t\theta_{ij})\, s_j - \cos((1-t)\theta_{ij})\, s_i\big)}{\sin\theta_{ij}}, \qquad \omega_i(t) = r_i^{\,t}
$$

The weighted path measure matches both endpoints:

$$
\rho_t = \sum_i a_i\, r_i^{\,t} \sum_j \frac{\pi_{ij}}{\sum_k \pi_{ik}}\, \delta_{\gamma_{ij}(t)}, \qquad \rho_0 = \sum_i a_i \delta_{s_i}, \qquad \rho_1 = \sum_{ij} \pi_{ij} \delta_{s_j}
$$

The fields that generate $$\rho_t$$ are *mass-weighted* conditional expectations, so an unweighted
flow-matching regression would converge to the wrong field:

$$
v_t(x) = \frac{\mathbb{E}\big[\,\omega(t)\, \dot X_t \mid X_t = x\,\big]}{\mathbb{E}\big[\,\omega(t) \mid X_t = x\,\big]}, \qquad g_t(x) = \frac{\mathbb{E}\big[\,\omega(t)\, \log r \mid X_t = x\,\big]}{\mathbb{E}\big[\,\omega(t) \mid X_t = x\,\big]}
$$

### Coupling: semi-relaxed entropic unbalanced OT

Within one plate, with $$a_i = 1/n_0$$, $$b_j = m/n_1$$ and cost $$C_{ij} = d_{\mathrm{FR}}^2(s_i, s_j)$$:

$$
\pi^\star = \operatorname*{arg\,min}_{\pi \ge 0}\ \langle C, \pi \rangle + \varepsilon\,\mathrm{KL}\big(\pi \,\|\, a \otimes b\big) + \tau_0\,\mathrm{KL}\big(\pi\mathbf{1} \,\|\, a\big) + \tau_1\,\mathrm{KL}\big(\pi^\top\mathbf{1} \,\|\, b\big), \qquad \tau_1 \gg \tau_0
$$

solved by generalized Sinkhorn iterations with $$K = \exp(-C/\varepsilon)$$:

$$
u \leftarrow \Big(\frac{a}{K v}\Big)^{\frac{\tau_0}{\tau_0 + \varepsilon}}, \qquad v \leftarrow \Big(\frac{b}{K^\top u}\Big)^{\frac{\tau_1}{\tau_1 + \varepsilon}}, \qquad \pi^\star = \operatorname{diag}(u)\, K \operatorname{diag}(v), \qquad r_i = \frac{(\pi^\star\mathbf{1})_i}{a_i}
$$

The entropic term has a biological reading: $$\varepsilon = 2\sigma^2$$, where $$\sigma$$ is the intrinsic noise
with which identical cells respond to the same perturbation.

### Training loss

Draw $$i \sim a$$, $$j \sim \pi^\star_{ij} / \sum_k \pi^\star_{ik}$$ and $$t \sim U[0, 1]$$, with $$\Pi_x$$ the
projection onto $$T_xS$$:

$$
\mathcal{L}(\theta) = \mathbb{E}_{(c,\pi),\ i,\ j,\ t}\Big[\, r_i^{\,t}\Big( \big\| \Pi_{X_t} v_\theta(t, X_t \mid c, \pi) - u_t \big\|^2 + \lambda_g \big( g_\theta(t, X_t \mid c, \pi) - \log r_i \big)^2 \Big) \Big]
$$

Its minimizers are exactly the weighted conditional expectations above, so its gradient matches that of the
intractable marginal loss.

### Inference

Control cells are pushed through the learned flow with a geometric Euler scheme that stays on the sphere:

$$
X_{n+1} = \operatorname{Exp}_{X_n}\!\big(\Delta\, \Pi_{X_n} v_\theta(t_n, X_n \mid c, \pi)\big), \qquad \log W_{n+1} = \log W_n + \Delta\, g_\theta(t_n, X_n \mid c, \pi)
$$

$$
\hat\nu_1^{c,\pi} = \frac{1}{N}\sum_{i=1}^{N} W_1^{i}\, \delta_{\psi(X_1^{i})}, \qquad \hat m(c, \pi) = \frac{1}{N}\sum_{i=1}^{N} W_1^{i}
$$

A stochastic version, with a learned score $$s_\phi$$, models cell-to-cell variability in the response:

$$
X_{n+1} = \operatorname{Exp}_{X_n}\!\Big(\Delta\, \Pi_{X_n}\big[v_\theta + \tfrac{\sigma^2}{2} s_\phi\big](t_n, X_n) + \sigma\sqrt{\Delta}\;\xi_n\Big), \qquad \xi_n \sim \mathcal{N}(0, I_{T_{X_n}S})
$$

### Combinations and transcriptome-wide Bliss synergy

Combinations are embedded symmetrically, and setting the interaction term to zero gives the additive reference:

$$
e(\pi_1 \oplus \pi_2) = \varphi(\pi_1) + \varphi(\pi_2) + \psi(\pi_1, \pi_2), \qquad \psi(\pi_1, \pi_2) = \psi(\pi_2, \pi_1)
$$

Bliss independence ($$m_{12} = m_1 m_2$$) corresponds to additive growth fields, which defines an additive
counterfactual:

$$
v^{\mathrm{add}}_t = v_t(\cdot \mid \pi_1) + v_t(\cdot \mid \pi_2), \qquad g^{\mathrm{add}}_t = g_t(\cdot \mid \pi_1) + g_t(\cdot \mid \pi_2)
$$

and two synergy scores, one for viability and one for the whole transcriptome:

$$
\mathrm{Syn}_{\mathrm{via}}(\pi_1, \pi_2) = \log \hat m_{12} - \log \hat m_{1} - \log \hat m_{2}, \qquad \mathrm{Syn}_{\mathrm{tx}}(\pi_1, \pi_2) = \mathrm{S}_\varepsilon\big(\hat\nu_1^{\,\pi_1 \oplus \pi_2},\ \hat\nu_1^{\,\mathrm{add}}\big)
$$

$$
\mathrm{S}_\varepsilon(\alpha, \beta) = \mathrm{OT}_\varepsilon(\alpha, \beta) - \tfrac12\mathrm{OT}_\varepsilon(\alpha, \alpha) - \tfrac12\mathrm{OT}_\varepsilon(\beta, \beta)
$$

### Experiment ranking

Untested perturbations are ranked by the disagreement of an ensemble of $$K$$ models:

$$
D(\pi) = \frac{2}{K(K-1)}\sum_{k < l} \mathrm{S}_\varepsilon\big(\hat\nu_1^{(k)}(\pi),\ \hat\nu_1^{(l)}(\pi)\big)
$$

with further posterior samples from cyclical SGLD:

$$
\theta_{k+1} = \theta_k - \eta_k \nabla_\theta \hat U(\theta_k) + \sqrt{2\eta_k/\tau}\;\xi_k, \qquad \hat U(\theta) = n\,\hat{\mathcal{L}}(\theta) - \log p(\theta)
$$
