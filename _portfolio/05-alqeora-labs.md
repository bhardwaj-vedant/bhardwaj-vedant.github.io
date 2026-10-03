---
title: "Alqeora Labs: Unbalanced Riemannian Flow Matching for Virtual Perturbation Screens"
collection: portfolio
order: 5
excerpt: "Founder & Lead Researcher, Alqeora Labs · Predicting how a population of human cells responds to a drug, a gene edit, or a combination, before the experiment is run."
meta: "Independent venture · Founder & Lead Researcher · Last updated October 3, 2026"
---

**Role:** Founder & Lead Researcher (independent venture)<br/>
**Last updated:** October 3, 2026

## 1. Executive summary

Alqeora Labs predicts how a population of human cells will respond to a drug, a gene edit, or a combination of
them, before the experiment is run. It outputs the full distribution of resulting cell states plus predicted
viability, and ranks which real experiments are worth paying for.

The engine is an **unbalanced, conditional, Riemannian flow-matching model**. It transports the control-cell
population to the perturbed population on the Fisher–Rao sphere of gene-expression profiles, while a learned
growth field accounts for cells that die or proliferate.

**What the customer gets:**

- **Virtual screen.** Cell line + perturbation (gene knockdown, compound and dose, or a pair) → predicted
  single-cell expression distribution and predicted viability, with uncertainty.
- **Synergy map.** For combinations, a principled synergy score that generalizes the pharmacologist's Bliss
  independence to whole-transcriptome responses ([Section 6](#6-dynamics-inference-and-product-features)).
- **Experiment ranking.** Untested perturbations ranked by how much running them would reduce model uncertainty
  (active learning), so each wet-lab rupee buys the most information.

**Why every piece of the math is load-bearing:**

| Mathematical tool | Biological fact that forces it |
| --- | --- |
| Optimal transport between measures | Sequencing destroys the cell, so you never observe the same cell before and after; only two populations |
| Unbalanced transport (Wasserstein–Fisher–Rao) | Perturbations kill some cells and make others divide, so total mass is not conserved |
| Fisher–Rao geometry on the simplex | Counts are multinomial samples; this is the geometry in which measurement noise is isotropic |
| Entropic regularization / Schrödinger bridges | Genetically identical cells respond stochastically; ε is literally response noise, and it also fixes the statistical rate of OT in high dimension |
| Conditional flow matching over perturbation embeddings | The perturbation space is combinatorial, so the model must generalize to perturbations it has never seen |

> **Honest framing for a reviewer.** Recent benchmarks found that deep perturbation models, including large
> single-cell foundation models, often fail to beat simple linear baselines on unseen perturbations. Alqeora's
> first deliverable is therefore not "a bigger model" but a rigorous, distribution-level benchmark that the
> method must win honestly. Losing to a linear model on mean expression while winning on viability,
> heterogeneity and combinations would still be a strong, publishable result.

## 2. Problem statement

*Drug discovery needs the conditional law of how a cell population changes under a perturbation it has never
tested, and the standard tools either collapse that law to a mean, assume paired data that cannot exist, or
force mass conservation that biology violates.*

**Setup.** Fix a cellular context $$c$$ (cell line, plate or batch) and a perturbation $$\pi$$ (gene knockdown,
compound with dose, or a set of them). Single-cell RNA sequencing gives two unpaired samples: control cells drawn
from a measure $$\nu_0^c$$ and perturbed cells drawn from $$\nu_1^{c,\pi}$$. These are *unnormalized* measures:
the perturbed population's total mass $$m(c, \pi)$$, relative to control, is its viability or fitness. The task
is to learn the map

$$
(c,\ \pi) \;\longmapsto\; \nu_1^{c,\pi} = m(c,\pi)\,\mu_1^{c,\pi}, \qquad \pi \in \Pi_{\text{unseen}} \cup \{\pi_1 \oplus \pi_2\}
$$

The space is combinatorial: about 20,000 genes give about $$2 \times 10^8$$ gene pairs, and thousands of
compounds multiply by doses and cell types. Exhaustive screening is impossible, so the value is in
generalization.

**Failure 1 — differential expression collapses distributions to means.** Pseudobulk tests (t-tests,
DESeq2-style models) compare average expression. A drug that pushes 30% of cells into apoptosis and leaves 70%
untouched has the same mean shift as one that nudges every cell slightly. Those are different biology and
different drugs. Response heterogeneity — resistant subpopulations, bimodal fates — is exactly what oncology
teams care about, and a mean discards it.

**Failure 2 — supervised learning needs pairs that cannot exist.** Sequencing lyses the cell, so no
(before, after) pair is ever observed. Any method that regresses a cell's perturbed state on its control state is
fitting a coupling it invented. Learning a response is intrinsically learning a transport between measures,
which is why optimal transport (Waddington-OT, CellOT) became the field's native tool.

**Failure 3 — balanced OT hallucinates transitions when cells die.** Classical OT requires equal total mass. If
a compound kills a sensitive subpopulation, balanced OT must still send that mass somewhere, so it "moves" dying
cells into surviving states and reports transitions that never happened. The correct model is unbalanced
transport, where the continuity equation has a source term:

$$
\partial_t \rho_t + \operatorname{div}(\rho_t v_t) = g_t\,\rho_t
$$

Here $$g_t > 0$$ is proliferation and $$g_t < 0$$ is death. The geometry of this equation is the
Wasserstein–Fisher–Rao (Hellinger–Kantorovich) distance of Chizat et al. and Liero–Mielke–Savaré.

**Failure 4 — Euclidean geometry on expression data is not noise-calibrated.** A cell's counts $$x$$ are a
multinomial sample of its true expression proportions $$p$$ on the simplex $$\Delta^{G-1}$$. Standard pipelines
take $$\log(1 + x)$$, then Euclidean PCA: an ad hoc choice under which low-count genes look far noisier than
high-count ones. The Fisher information of the multinomial is the Fisher–Rao metric, so it is the unique geometry
(up to scale, by Čencov's theorem) in which equal distances mean equal statistical distinguishability. The
square-root map that makes it Euclidean-like is also the classical variance-stabilizing transform for counts.

$$
g^{\mathrm{FR}}_p(u, v) = \sum_{i=1}^{G} \frac{u_i v_i}{p_i}, \qquad u, v \in T_p\Delta = \{u : \textstyle\sum_i u_i = 0\}
$$

**Failure 5 — plain OT is statistically cursed in $$G = 2{,}000$$ dimensions.** The empirical Wasserstein
distance converges at rate $$n^{-1/d}$$, useless at $$d$$ in the thousands. Entropic regularization changes the
statistics, not just the computation: the entropic OT cost converges at the parametric rate $$n^{-1/2}$$, with
constants that grow with dimension and $$1/\varepsilon$$ (Genevay et al. 2019; Mena and Niles-Weed 2019). Low
intrinsic dimension of transcriptomic states, plus entropic couplings, is what makes the problem learnable from
tens of thousands of cells.

**Alqeora's answer, one line each:**

1. Work on the Fisher–Rao sphere, where noise is isotropic and geodesics are closed-form.
2. Learn a velocity field *and* a growth field, so death and proliferation are modelled, not hallucinated.
3. Couple control and perturbed cells by entropic unbalanced OT *within the same plate*, which handles mass
   change and removes batch confounding.
4. Condition on learned embeddings of genes, compounds and doses, and compose them for combinations.
5. Report calibrated uncertainty and use it to choose the next experiments.

## 3. The measure space and geometry

*Cells live on the positive orthant of a radius-2 sphere in $$\mathbb{R}^G$$ ($$G = 2{,}000$$ genes), populations
are finite unnormalized measures on it, and the square-root map makes the Fisher–Rao geometry of expression
exactly the round-sphere geometry.*

**From counts to the sphere.** Restrict to $$G$$ highly variable genes, normalize a cell's counts to proportions
$$p \in \Delta^{G-1}$$, and apply the square-root embedding. It is an isometry from $$(\Delta, g^{\mathrm{FR}})$$
onto the positive orthant of the sphere of radius 2:

$$
\phi : \Delta^{G-1} \to S^{G-1}_{+}(2), \qquad \phi(p) = 2\sqrt{p}, \qquad d_{\mathrm{FR}}(p, q) = 2\arccos\Big(\sum_{i=1}^{G}\sqrt{p_i q_i}\Big)
$$

The argument of arccos is the Bhattacharyya coefficient, so the Fisher–Rao distance has a direct statistical
reading: how distinguishable two expression programs are from finite samples.

**Closed-form geometry.** With $$R = 2$$ and $$\theta = \arccos(\langle s_0, s_1\rangle / R^2)$$, the exponential
map, logarithm and geodesic are:

$$
\begin{aligned}
\operatorname{Exp}_s(u) &= \cos\!\big(\tfrac{\|u\|}{R}\big)\, s + R \sin\!\big(\tfrac{\|u\|}{R}\big) \frac{u}{\|u\|}, \qquad u \in T_sS = \{u : \langle u, s\rangle = 0\},\\
\gamma(t) &= \frac{\sin((1-t)\theta)\, s_0 + \sin(t\theta)\, s_1}{\sin\theta}, \qquad \operatorname{Log}_{s_0}(s_1) = R\,\theta \,\frac{s_1 - \cos\theta\, s_0}{\|s_1 - \cos\theta\, s_0\|}
\end{aligned}
$$

Two facts make this geometry unusually well-behaved:

- **No cut-locus problems.** Two points in the positive orthant have non-negative inner product, so
  $$\theta \le \pi/2$$. The cut locus of the sphere is at $$\theta = \pi$$, so geodesics between real cells are
  always unique and smooth, and conditional flow-matching targets are globally defined.
- **Geodesic convexity.** For $$t \in [0, 1]$$ the coefficients $$\sin((1-t)\theta)/\sin\theta$$ and
  $$\sin(t\theta)/\sin\theta$$ are non-negative, so geodesics between cells stay in the orthant, meaning every
  interpolated state is a valid expression profile.

**A global map back to the simplex.** At inference a learned flow may leave the orthant. The fold
$$\psi(s) = (s \odot s)/R^2$$, renormalized, maps the entire sphere smoothly onto the simplex. So the model can
sample on $$S^{G-1}$$ freely, and every output is still a valid proportion vector, with no clipping step to bias
the result.

**Measures.** For context $$c$$, control and perturbed populations are empirical, unnormalized measures on the
sphere:

$$
\nu_0^{c} = \frac{1}{n_0}\sum_{i=1}^{n_0} \delta_{s_i}, \qquad \nu_1^{c,\pi} = \frac{m(c,\pi)}{n_1}\sum_{j=1}^{n_1} \delta_{s_j}, \qquad m(c,\pi) = \frac{\nu_1^{c,\pi}(S)}{\nu_0^{c}(S)}
$$

**Where the mass $$m$$ comes from.** In drug screens, use cells recovered per well relative to vehicle-treated
wells (the sci-Plex design reports this, and it is a standard viability proxy). In pooled CRISPR screens, use
each guide's cell count relative to its representation in the starting library. Where neither is available, set
$$m = 1$$ and fit only the shape of the response, stating this explicitly in results.

**Honest caveat on noise.** A single cell's $$\hat p$$ is a noisy multinomial estimate of its true state, at
depths of a few thousand reads. The geometry is the right one for that noise, but noise is still present in both
source and target. The MVP models observed $$\hat p$$ directly; a research extension models a latent true state
on the sphere with a multinomial observation layer.

**Conditioning.** Each sample carries a context and a perturbation embedding:

- **Context $$c$$:** a learned cell-line embedding plus the plate or batch ID (used for coupling,
  [Section 5](#5-the-loss-function-and-optimization), not as a model input at test time).
- **Genetic perturbations:** a fixed embedding of the target gene, from Gene Ontology graph features or a protein
  language model of its product. This is what lets the model generalize to genes never perturbed in training.
- **Compounds:** a molecular fingerprint or small graph network embedding, plus log dose.
- **Combinations:** a symmetric composition, where setting the interaction term $$\psi$$ to zero gives the
  additive reference used for synergy in [Section 6](#6-dynamics-inference-and-product-features):

$$
e(\pi_1 \oplus \pi_2) = \varphi(\pi_1) + \varphi(\pi_2) + \psi(\pi_1, \pi_2), \qquad \psi(\pi_1, \pi_2) = \psi(\pi_2, \pi_1)
$$

## 4. The continuity equation with growth

*Alqeora learns two fields at once — a tangent velocity $$v_t$$ that moves cell states and a scalar growth rate
$$g_t$$ that creates or destroys mass — and the key technical point is that, with growth, the marginal fields are
mass-weighted conditional expectations, so the regression must be weighted.*

**Weighted particle dynamics.** A cell is a particle $$X_t$$ on the sphere carrying a mass $$W_t$$. Both evolve
under the learned fields, conditioned on context and perturbation:

$$
\frac{dX_t}{dt} = v_t\big(X_t \mid c, \pi\big) \in T_{X_t}S, \qquad \frac{d\log W_t}{dt} = g_t\big(X_t \mid c, \pi\big), \qquad \rho_t = \mathbb{E}\big[\,W_t\,\delta_{X_t}\big]
$$

**Unbalanced continuity equation.** The measure $$\rho_t$$ then satisfies the continuity equation with a
reaction term, in weak form on the sphere:

$$
\partial_t \rho_t + \operatorname{div}_g(\rho_t v_t) = g_t\,\rho_t
\quad\Longleftrightarrow\quad
\frac{d}{dt}\int_S \varphi\, d\rho_t = \int_S \Big( \langle \nabla_g \varphi, v_t \rangle_g + g_t\, \varphi \Big)\, d\rho_t \quad \forall \varphi \in C^\infty(S)
$$

The proof is one line: differentiate $$\mathbb{E}[W_t \varphi(X_t)]$$ with the product and chain rules. Taking
$$\varphi \equiv 1$$ shows that total mass changes only through $$g$$, which is how viability enters the model.

**Wasserstein–Fisher–Rao geometry.** Among all $$(v, g)$$ connecting $$\nu_0$$ to $$\nu_1$$, the action-minimizing
ones define the WFR (Hellinger–Kantorovich) distance. In one common normalization:

$$
\mathrm{WFR}_\delta^2(\nu_0, \nu_1) = \inf_{(\rho, v, g)} \int_0^1\!\!\int_S \Big( \|v_t\|_g^2 + \frac{\delta^2}{4}\, g_t^2 \Big)\, d\rho_t\, dt
$$

The length scale $$\delta$$ is biologically meaningful: beyond a distance proportional to $$\delta$$ it is
cheaper to kill cells in one state and grow them in another than to transport them. In plain terms, $$\delta$$
separates "cells changed state" from "one subpopulation died while another expanded." Treat it as a
hyperparameter and report sensitivity to it.

**Conditional paths with growth.** Let $$\pi_{ij}$$ be an unbalanced coupling between control cell $$s_i$$
(mass $$a_i$$) and perturbed cell $$s_j$$ ([Section 5](#5-the-loss-function-and-optimization)), and let
$$r_i = (\sum_j \pi_{ij})/a_i$$ be source cell $$i$$'s mass ratio. Along each pair, move on the geodesic and grow
exponentially:

$$
X_t = \gamma_{s_i \to s_j}(t), \quad u_t = \dot\gamma_{s_i \to s_j}(t) = \frac{\theta_{ij}\big(\cos(t\theta_{ij})\, s_j - \cos((1-t)\theta_{ij})\, s_i\big)}{\sin\theta_{ij}}, \qquad \omega_i(t) = r_i^{\,t}, \quad \frac{d}{dt}\log\omega_i = \log r_i
$$

Draw a control cell $$i$$ with probability $$a_i$$, then a partner $$j$$ with probability
$$\pi_{ij} / \sum_k \pi_{ik}$$. The weighted path measure
$$\rho_t = \sum_i a_i r_i^t \sum_j (\pi_{ij} / \sum_k \pi_{ik})\, \delta_{\gamma_{ij}(t)}$$ matches both endpoints:
at $$t = 0$$ it is $$\sum_i a_i \delta_{s_i}$$, the control population, and at $$t = 1$$ it is
$$\sum_{ij} \pi_{ij} \delta_{s_j}$$, the perturbed population. The weights $$r_i^t$$ are bounded by the largest
proliferation ratio, so the estimator has low variance. Sampling pairs proportional to $$\pi_{ij}$$ instead would
need weights $$r_i^{t-1}$$, which explode for dying cells.

**Weighted marginalization theorem.** The fields that generate $$\rho_t$$ are mass-weighted conditional
expectations of the pair velocities and growth rates:

$$
v_t(x) = \frac{\mathbb{E}\big[\,\omega(t)\, \dot X_t \mid X_t = x\,\big]}{\mathbb{E}\big[\,\omega(t) \mid X_t = x\,\big]}, \qquad g_t(x) = \frac{\mathbb{E}\big[\,\omega(t)\, \log r \mid X_t = x\,\big]}{\mathbb{E}\big[\,\omega(t) \mid X_t = x\,\big]}
$$

The proof uses the weak form above:
$$\frac{d}{dt} \mathbb{E}[\omega(t) \varphi(X_t)] = \mathbb{E}[\omega(t)(\langle \nabla\varphi, \dot X_t\rangle + \log r \cdot \varphi)]$$,
then condition on $$X_t$$. The practical consequence is easy to get wrong: in the unbalanced setting, an
unweighted flow-matching regression converges to the wrong field, because it ignores that dying cells carry less
mass over time. When every $$r_i = 1$$ the weights vanish and this reduces to standard Riemannian conditional
flow matching.

## 5. The loss function and optimization

*Train with mass-weighted Riemannian conditional flow matching on pairs drawn from a semi-relaxed entropic
unbalanced OT coupling, computed only within the same experimental plate; the observed viability fixes how many
cells died, and the coupling infers which ones.*

**Coupling: semi-relaxed entropic unbalanced OT.** For one plate and one perturbation, take $$n_0$$ control cells
(masses $$a_i = 1/n_0$$) and $$n_1$$ perturbed cells (masses $$b_j = m/n_1$$, so their total is the observed
viability $$m$$). With cost $$C_{ij} = d_{\mathrm{FR}}^2(s_i, s_j)$$, solve:

$$
\pi^\star = \operatorname*{arg\,min}_{\pi \ge 0}\ \langle C, \pi \rangle + \varepsilon\,\mathrm{KL}\big(\pi \,\|\, a \otimes b\big) + \tau_0\,\mathrm{KL}\big(\pi\mathbf{1} \,\|\, a\big) + \tau_1\,\mathrm{KL}\big(\pi^\top\mathbf{1} \,\|\, b\big), \qquad \tau_1 \gg \tau_0
$$

A finite $$\tau_0$$ lets each control cell's outgoing mass $$r_i a_i$$ deviate from $$a_i$$, which is how death
and proliferation are assigned to specific cell states. A very large $$\tau_1$$ pins the perturbed marginal, so
the total mass equals the measured viability. Solve with the generalized Sinkhorn iterations of Chizat et al.
(2018), $$K = \exp(-C/\varepsilon)$$:

$$
u \leftarrow \Big(\frac{a}{K v}\Big)^{\frac{\tau_0}{\tau_0 + \varepsilon}}, \qquad v \leftarrow \Big(\frac{b}{K^\top u}\Big)^{\frac{\tau_1}{\tau_1 + \varepsilon}}, \qquad \pi^\star = \operatorname{diag}(u)\, K \operatorname{diag}(v)
$$

Run it in the log domain. Read off the per-cell mass ratios $$r_i = (\pi^\star\mathbf{1})_i / a_i$$, which become
the growth targets.

**Why within-plate coupling matters.** Single-cell data carry strong batch effects. Coupling a perturbed cell to
a control from a different plate makes the model learn the batch difference as if it were drug response.
Restricting couplings to the same plate (and the same cell line) removes this confound by construction. It is a
small engineering choice that many published pipelines get wrong.

**The loss.** Draw $$i \sim a$$, then $$j \sim \pi^\star_{ij} / \sum_k \pi^\star_{ik}$$, then
$$t \sim U[0, 1]$$. With the geodesic targets of [Section 4](#4-the-continuity-equation-with-growth) and
$$\Pi_x$$ the projection onto $$T_xS$$:

$$
\mathcal{L}(\theta) = \mathbb{E}_{(c,\pi),\ i,\ j,\ t}\Big[\, r_i^{\,t}\Big( \big\| \Pi_{X_t} v_\theta(t, X_t \mid c, \pi) - u_t \big\|^2 + \lambda_g \big( g_\theta(t, X_t \mid c, \pi) - \log r_i \big)^2 \Big) \Big]
$$

**Why it works.** For each $$t$$ this is a weighted least-squares problem whose minimizers are exactly the
weighted conditional expectations of Section 4. Expanding the squares, the $$\theta$$-dependent cross terms match
those of the intractable marginal loss by the tower property under the $$r^t$$-weighted measure. So
$$\nabla_\theta$$ of the conditional loss equals $$\nabla_\theta$$ of the marginal loss, as in balanced flow
matching, but only with the weights.

**The Schrödinger-bridge reading.** The regularized unbalanced OT problem is equivalent to entropy minimization
relative to a branching Brownian motion, in which particles diffuse, die and split (Baradat and Lavenant). So
$$\varepsilon = 2\sigma^2$$ has a biological meaning: $$\sigma$$ is the intrinsic stochasticity with which
genetically identical cells respond to the same perturbation. Tune it on validation data and report it; it is a
biological parameter, not just a smoothing knob. For a stochastic sampler, add tangent noise around each
geodesic, $$X_t = \operatorname{Exp}_{\gamma(t)}(\sigma\sqrt{t(1-t)}\, \xi)$$, and train a score head as in
simulation-free score and flow matching. On the sphere this is a small-$$\sigma$$ approximation to the true
Brownian bridge, and should be reported as such.

**Optimization recipe.**

- **Batches:** 8 perturbation groups per step, each with 128 control and 128 perturbed cells from one plate.
  Couplings are solved per group, so the Sinkhorn cost stays $$O(128^2)$$.
- **Optimizer:** AdamW, learning rate $$3 \cdot 10^{-4}$$ with cosine decay, weight decay $$10^{-4}$$, EMA of
  weights at 0.999, about 200k steps.
- **Hyperparameters to sweep:** $$\varepsilon$$ (response noise), $$\tau_0$$ (how freely mass is reassigned),
  $$\lambda_g$$ (growth-loss weight) and $$\delta$$ implicitly through $$\tau_0/\varepsilon$$. Select on held-out
  perturbations, never on training perturbations.
- **Network:** an MLP of 4–6 residual blocks, width 1,024, with Fourier time features and FiLM conditioning on
  (cell line, perturbation embedding). Two heads: a $$G$$-dimensional ambient velocity (projected to the tangent
  space) and a scalar growth rate.

## 6. Dynamics, inference and product features

*At inference Alqeora pushes real control cells through the learned weighted flow; the endpoints are the
predicted perturbed population, the mean weight is the predicted viability, and the same machinery yields
synergy scores, resistance maps and an experiment-ranking signal.*

### 6.1 Weighted-particle ODE (deterministic prediction)

Take held-out control cells of the target context, embed them on the sphere, and integrate with a geometric
Euler scheme so every step stays on the sphere:

$$
X_{n+1} = \operatorname{Exp}_{X_n}\!\big(\Delta\, \Pi_{X_n} v_\theta(t_n, X_n \mid c, \pi)\big), \qquad \log W_{n+1} = \log W_n + \Delta\, g_\theta(t_n, X_n \mid c, \pi)
$$

The outputs are the predicted population and viability. The fold $$\psi$$ maps endpoints back to valid
expression proportions:

$$
\hat\nu_1^{c,\pi} = \frac{1}{N}\sum_{i=1}^{N} W_1^{i}\, \delta_{\psi(X_1^{i})}, \qquad \hat m(c, \pi) = \frac{1}{N}\sum_{i=1}^{N} W_1^{i}
$$

### 6.2 Stochastic sampler (response noise)

With the score head, simulate the bridge as a geodesic random walk, which converges weakly to the Riemannian
diffusion as $$\Delta \to 0$$. This produces cell-to-cell variability in response rather than a deterministic
map, which matters when predicting fractions of cells reaching a fate:

$$
X_{n+1} = \operatorname{Exp}_{X_n}\!\Big(\Delta\, \Pi_{X_n}\big[v_\theta + \tfrac{\sigma^2}{2} s_\phi\big](t_n, X_n) + \sigma\sqrt{\Delta}\;\xi_n\Big), \qquad \xi_n \sim \mathcal{N}(0, I_{T_{X_n}S})
$$

### 6.3 Anchoring the null perturbation

Include training groups that couple one half of a plate's control cells to the other half, labelled with the
null perturbation $$\varnothing$$. This anchors $$v(\cdot \mid \varnothing) \approx 0$$ and
$$g(\cdot \mid \varnothing) \approx 0$$, so "no effect" is a learned, testable prediction rather than an
accident. It also gives a calibration check: the model's predicted effect for $$\varnothing$$ on held-out plates
estimates its noise floor.

### 6.4 Unseen perturbations

Generalization comes entirely from the perturbation embedding: genes via Gene Ontology or
protein-language-model features of their products, compounds via molecular fingerprints plus log dose. Evaluate
three regimes separately, in increasing difficulty: unseen doses of seen compounds, unseen genes or compounds,
and unseen cell lines. Do not average them; the last is far harder and pooled numbers hide that.

### 6.5 Combinations and a transcriptome-wide Bliss synergy score

Pharmacologists call two drugs independent under Bliss when survival multiplies: $$m_{12} = m_1 \cdot m_2$$,
i.e. log-viabilities add. In Alqeora's model log-viability is the time-integral of the growth rate, so, cell by
cell along each path, Bliss independence corresponds to additivity of growth fields. Define the additive
counterfactual by adding fields in the tangent space at each point:

$$
v^{\mathrm{add}}_t = v_t(\cdot \mid \pi_1) + v_t(\cdot \mid \pi_2), \qquad g^{\mathrm{add}}_t = g_t(\cdot \mid \pi_1) + g_t(\cdot \mid \pi_2)
$$

Then report two synergy scores, one for viability and one for the whole transcriptome:

$$
\mathrm{Syn}_{\mathrm{via}}(\pi_1, \pi_2) = \log \hat m_{12} - \log \hat m_{1} - \log \hat m_{2}, \qquad \mathrm{Syn}_{\mathrm{tx}}(\pi_1, \pi_2) = \mathrm{S}_\varepsilon\big(\hat\nu_1^{\,\pi_1 \oplus \pi_2},\ \hat\nu_1^{\,\mathrm{add}}\big)
$$

Here $$\mathrm{S}_\varepsilon$$ is the (unbalanced) Sinkhorn divergence,
$$\mathrm{S}_\varepsilon(\alpha, \beta) = \mathrm{OT}_\varepsilon(\alpha, \beta) - \tfrac12\mathrm{OT}_\varepsilon(\alpha, \alpha) - \tfrac12\mathrm{OT}_\varepsilon(\beta, \beta)$$.
It is non-negative and metrizes weak convergence (Feydy et al. 2019; Séjourné et al. 2019 for the unbalanced
case). The viability score reduces exactly to classical Bliss excess. The transcriptome score is the new part:
it detects combinations whose state response is non-additive even when viability looks additive. Note that
adding tangent fields on a curved space is a modelling convention, accurate when effects are small; state that
in any write-up.

### 6.6 Resistance maps

The growth field $$g_t(x \mid c, \pi)$$ evaluated on control cells assigns each pre-existing cell state a
predicted survival. Clustering states with high predicted survival under a lethal compound flags candidate
pre-existing resistant subpopulations, a question oncology teams pay to answer.

### 6.7 Experiment ranking (active learning)

Train an ensemble of $$K$$ models (or draw SGLD posterior samples, [Section 8](#8-diagnostics)). For each
untested perturbation $$\pi$$, score how much the members disagree:

$$
D(\pi) = \frac{2}{K(K-1)}\sum_{k < l} \mathrm{S}_\varepsilon\big(\hat\nu_1^{(k)}(\pi),\ \hat\nu_1^{(l)}(\pi)\big)
$$

Rank candidates by $$D$$ and pick a batch greedily with a diversity penalty (maximize $$D$$ minus similarity to
already-chosen perturbations in embedding space). This is the product's economic engine: it tells a lab which 50
experiments, out of 50,000 candidates, would most reduce uncertainty about the rest.

## 7. MVP architecture and tech stack

*The MVP trains on one 24 GB GPU in hours to a day per experiment: the datasets are public, about
$$10^5$$–$$10^6$$ cells at $$G = 2{,}000$$, and the hard parts are correct weighting, batch handling and honest
evaluation, not compute.*

**Datasets** (public; sizes approximate, check each paper).

| Dataset | What it contains | What it tests |
| --- | --- | --- |
| Norman et al. 2019 (*Science*) | CRISPR activation in K562 cells: roughly 100 single genes and 130 gene pairs | Genetic combinations and synergy |
| Srivatsan et al. 2020, sci-Plex (*Science*) | 188 compounds × 4 doses × 3 cancer cell lines, with cell counts per well | Drugs, doses and the unbalanced (viability) model |
| Replogle et al. 2022 (*Cell*) | Genome-scale CRISPR interference Perturb-seq in K562 and RPE1 | Unseen genes; transfer across cell lines |
| Tahoe-100M (2025) | About 100M cells, about 50 cancer cell lines, about 1,100 drug treatments | Scale-up and unseen cell lines (use subsets) |

The scverse package **pertpy** includes loaders and analysis tools for several of these, which saves a week of
data wrangling.

**Core libraries.**

| Library | Role |
| --- | --- |
| scanpy, anndata, pertpy | Loading, QC, highly-variable-gene selection, sparse matrices, perturbation metadata |
| PyTorch 2.x | Model, training, geometry (all sphere operations are a few lines) |
| POT (Python Optimal Transport) | `ot.unbalanced.sinkhorn_unbalanced` for couplings; Sinkhorn divergences for evaluation |
| Geomstats | Hypersphere with exp/log/distance, used only as ground truth in unit tests |
| torchdiffeq | Adaptive ODE solvers in the ambient space with projection, for cross-checking the geometric Euler sampler |
| RDKit | Morgan fingerprints for compound embeddings |
| Gene Ontology tools or protein-language-model embeddings | Gene embeddings for unseen-gene generalization |
| GEARS, CPA, CellOT, scGen implementations | Published baselines, run with their authors' code |
| Hydra + Weights & Biases | Configs and experiment tracking for every ablation |

**Numerical engineering notes.**

- **Stable angles.** arccos is ill-conditioned near 1, which is exactly where nearby cells sit. For per-pair
  targets use $$\theta = 2\,\operatorname{atan2}(\|x - y\|, \|x + y\|)$$, which is exact on the sphere and stable
  everywhere. arccos is acceptable for the coupling's cost matrix, where small errors do not matter.
- **Sparsity.** Keep counts sparse in AnnData and form $$\sqrt{p}$$ per minibatch on the GPU; never densify the
  full matrix.
- **Weight hygiene.** Clamp $$r_i$$ away from 0 before taking logs, and track the spread of $$r_i^t$$ per batch
  ([Section 8](#8-diagnostics)).

**Code skeleton.** One plate-and-perturbation group's loss, plus the predictor. The model returns an ambient
velocity of size $$G$$ and a scalar growth rate.

```python
import torch, ot

R = 2.0

def to_sphere(p):                         # Fisher-Rao isometry: simplex -> radius-2 sphere
    return R * p.clamp_min(0).sqrt()

def to_simplex(s):                        # global fold: whole sphere -> simplex
    p = s.pow(2)
    return p / p.sum(-1, keepdim=True)

def angle(x, y):                          # numerically stable great-circle angle
    return 2 * torch.atan2((x - y).norm(dim=-1), (x + y).norm(dim=-1))

def tangent(s, v):                        # project ambient vectors onto T_s S
    return v - (v * s).sum(-1, keepdim=True) * s / R**2

def sphere_exp(s, u):
    n = u.norm(dim=-1, keepdim=True).clamp_min(1e-12)
    return torch.cos(n / R) * s + R * torch.sin(n / R) * u / n

def fr_cost(s0, s1):                      # pairwise squared Fisher-Rao distance
    cos = (s0 @ s1.T / R**2).clamp(-1.0, 1.0)
    return (R * torch.arccos(cos)) ** 2

def geodesic_target(s0, s1, t):           # point and velocity on the great circle
    th = angle(s0, s1)[:, None].clamp_min(1e-6)
    t = t[:, None]
    st = (torch.sin((1 - t) * th) * s0 + torch.sin(t * th) * s1) / torch.sin(th)
    dst = th * (torch.cos(t * th) * s1 - torch.cos((1 - t) * th) * s0) / torch.sin(th)
    return st, dst

def group_loss(model, ctrl, pert, cond, mass, eps, tau0, tau1, lam_g):
    """One plate, one perturbation. ctrl: (n0, G), pert: (n1, G) expression proportions."""
    s0, s1 = to_sphere(ctrl), to_sphere(pert)
    kw = dict(dtype=s0.dtype, device=s0.device)
    a = torch.full((len(s0),), 1.0 / len(s0), **kw)
    b = torch.full((len(s1),), mass / len(s1), **kw)    # total mass = observed viability
    C = fr_cost(s0, s1)
    with torch.no_grad():
        pi = ot.unbalanced.sinkhorn_unbalanced(
            a, b, C / C.max(), reg=eps, reg_m=(tau0, tau1), method="sinkhorn_stabilized")
    row = pi.sum(1)
    r = (row / a).clamp_min(1e-6)                        # per-control-cell mass ratio
    j = torch.multinomial(pi / row[:, None].clamp_min(1e-12), 1).squeeze(1)  # j | i
    t = torch.rand(len(s0), **kw)
    st, v_star = geodesic_target(s0, s1[j], t)           # i ranges over all control cells
    v_hat, g_hat = model(t, st, cond)
    w = r.pow(t)                                         # mass weights r_i^t
    v_err = (tangent(st, v_hat) - v_star).pow(2).sum(-1)
    g_err = (g_hat.squeeze(-1) - r.log()).pow(2)
    return (w * (v_err + lam_g * g_err)).mean()

@torch.no_grad()
def predict(model, ctrl, cond, n_steps=50):
    s = to_sphere(ctrl)
    log_w = torch.zeros(len(s), dtype=s.dtype, device=s.device)
    for k in range(n_steps):
        t = torch.full((len(s),), k / n_steps, dtype=s.dtype, device=s.device)
        v, g = model(t, s, cond)
        s = sphere_exp(s, tangent(s, v) / n_steps)
        log_w += g.squeeze(-1) / n_steps
    w = log_w.exp()
    return to_simplex(s), w, w.mean()                  # cells, weights, predicted viability
```

A training step sums `group_loss` over the 8 groups in a batch, including null-perturbation groups
([Section 6.3](#63-anchoring-the-null-perturbation)), then steps AdamW with gradient clipping at norm 1.

**12-week build plan, each phase gated by a measurable criterion.**

1. **Weeks 1–2: data and baselines.** Pipelines for Norman and sci-Plex; baselines: no change, mean shift, a
   linear model, plus GEARS (genetic), CPA (drugs) and CellOT. *Gate:* published baseline numbers reproduced
   within tolerance.
2. **Weeks 3–4: balanced Euclidean flow matching in PCA space.** *Gate:* at least matches mean shift on held-out
   doses.
3. **Weeks 5–6: Fisher–Rao sphere and within-plate coupling.** *Gate:* clean ablation against log1p-PCA geometry
   and against cross-plate coupling.
4. **Weeks 7–8: unbalanced model on sci-Plex.** Growth head and weighted loss. *Gate:* viability correlation on
   held-out compounds, plus a documented case where balanced OT invents transitions that the unbalanced model
   attributes to cell death.
5. **Weeks 9–10: combinations on Norman.** *Gate:* beats the additive reference and GEARS on held-out pairs, or
   an honest negative result with analysis.
6. **Weeks 11–12: active learning, write-up, demo.** Retrospective simulation: hide most perturbations, let the
   model choose which to "run", measure error reduction against random choice. Ship the preprint, repository and
   a small demo. *Gate:* model-chosen experiments reduce held-out error faster than random.

## 8. Diagnostics

*The failure modes that matter here are not GAN-style mode collapse but degenerate mass weights, batch leakage,
copying the nearest training perturbation, predicting a generic stress response, and uncertainty that does not
track error; each gets its own monitor.*

### 8.1 The generic-response trap

Many perturbations share a common stress signature. A model that predicts "the average perturbation effect" for
every input can score well on correlation-of-mean-change metrics while knowing nothing specific. Always report
results against the **mean training-perturbation effect** baseline, and against a **nearest-neighbour
perturbation** baseline (the observed response of the most similar training perturbation in embedding space).
Gains over these two baselines are the only gains that show the model learned perturbation-specific biology.

### 8.2 Uncertainty that tracks error

The active-learning claim in [Section 6.7](#67-experiment-ranking-active-learning) stands or falls on whether
ensemble disagreement $$D(\pi)$$ predicts actual error. On held-out perturbations, report the Spearman
correlation between $$D(\pi)$$ and the Sinkhorn divergence of the prediction from the truth. If it is weak, the
experiment ranking is not trustworthy, and the product claim must be softened. For posterior samples beyond a
5-member ensemble, run cyclical SGLD on the parameters:

$$
\theta_{k+1} = \theta_k - \eta_k \nabla_\theta \hat U(\theta_k) + \sqrt{2\eta_k/\tau}\;\xi_k, \qquad \hat U(\theta) = n\,\hat{\mathcal{L}}(\theta) - \log p(\theta)
$$

The flow-matching loss is not a log-likelihood, so treat $$\tau$$ as a calibration knob tuned on held-out error,
not as a Bayesian temperature.

### 8.3 Spectral diagnostics with Lanczos

Both use only matrix-vector products, so they scale to $$G = 2{,}000$$:

- **Flow-map Jacobian.** Golub–Kahan Lanczos on $$J^\top J$$, with $$J = \partial X_1/\partial X_0$$ obtained by
  JVPs and VJPs through the sampler. Near-zero singular values mean the flow crushes control-cell diversity into
  a few states, which is the real "collapse" mode for a transport model. Compare against the diversity of the
  observed perturbed population.
- **Loss Hessian.** Top eigenvalues by Hessian-vector products (forward-over-reverse in `torch.func`), tracked
  against $$2/\eta$$ to explain loss spikes, as in edge-of-stability analyses.

### 8.4 Monitoring dashboard

| Signal | How it is computed | Alarm |
| --- | --- | --- |
| Weight degeneracy (training) | ESS of the $$r_i^t$$ weights per group, $$(\sum w)^2/\sum w^2$$ divided by group size | Below 0.2 |
| Viability calibration | Predicted $$\hat m$$ against observed $$m$$ on held-out plates and doses | Calibration slope far from 1, or $$\hat m(\varnothing)$$ far from 1 |
| Null-perturbation noise floor | Sinkhorn divergence between predicted $$\varnothing$$ response and held-out controls | Comparable to real perturbation effects |
| Batch leakage | Classifier accuracy predicting plate ID from predicted cells | Well above chance |
| Copying | Divergence between prediction and nearest training perturbation's observed population | Prediction no closer to truth than the copy |
| Flow collapse | Smallest singular values of the flow Jacobian; predicted vs observed population diversity | Predicted diversity well below observed |
| Uncertainty quality | Spearman correlation of $$D(\pi)$$ with actual held-out error | Below about 0.3 |

## 9. Validation protocol

*Alqeora is scored on held-out perturbations, at the distribution level, against baselines chosen to be hard to
beat, with perturbations (not cells) as the unit of statistical replication. A method that only wins on cells
from perturbations it has seen has not learned anything sellable.*

**Splits, reported separately and never pooled.**

1. **Unseen doses** of seen compounds (sci-Plex): the easiest regime.
2. **Unseen perturbations:** genes never perturbed in training (Replogle), compounds never seen (sci-Plex, Tahoe
   subsets).
3. **Combinations** (Norman), in three tiers: both single perturbations seen, one seen, none seen.
4. **Unseen cell lines:** leave one cell line out (sci-Plex's three lines; Tahoe subsets). Expect this to be
   hard, and say so.

Hold out at the plate level, so no test plate's controls leak into training couplings.

**Metrics.**

- **Distribution level:** unbalanced Sinkhorn divergence and energy distance between predicted and observed
  populations. For MMD, use a Gaussian kernel on the *chordal* (ambient Euclidean) distance between sphere points.
  It is positive definite because it is the restriction of a PD kernel on $$\mathbb{R}^G$$, whereas a Gaussian
  kernel on the geodesic distance is not PD in general (Feragen et al. 2015).
- **Mean level** (for comparability with the literature): correlation of predicted vs observed change in mean
  expression over the top differentially expressed genes, plus the fraction of those genes whose direction is
  predicted correctly. Always shown next to the generic-response baselines of
  [Section 8.1](#81-the-generic-response-trap).
- **Heterogeneity:** cluster the observed perturbed cells; compare predicted and observed fractions of cells per
  response cluster.
- **Viability:** rank and linear correlation of $$\log \hat m$$ with $$\log m$$; for combinations, sign accuracy
  and correlation of predicted Bliss excess with observed Bliss excess.
- **Perturbation retrieval:** given a prediction, rank all candidate perturbations' observed populations by
  divergence; report the rank of the true one. This is the metric most robust to the generic-response trap.

**Statistics.** Bootstrap confidence intervals and paired tests over *perturbations*, not cells: cells within a
perturbation are not independent replicates, and treating them as such inflates significance dramatically.

**Baselines.** No change; mean shift per perturbation (where seen); mean training-perturbation effect;
nearest-neighbour perturbation; a linear model on perturbation embeddings; GEARS; CPA; balanced CellOT; scGen. Run
published methods with their authors' code and recommended settings.

**Pre-registered ablations.** Write each hypothesis down before running it, and publish negative results.

| Experiment | Arms | Hypothesis it tests |
| --- | --- | --- |
| Geometry | Fisher–Rao sphere vs log1p-PCA Euclidean | Noise-calibrated geometry improves held-out accuracy |
| Mass | Balanced vs unbalanced (growth head) | Modelling death removes spurious transitions and predicts viability |
| Weighting | Mass-weighted vs unweighted loss | The weighted marginalization theorem matters in practice |
| Coupling scope | Within-plate vs cross-plate coupling | Batch confounding is a major error source |
| Coupling type | Independent vs exact OT vs entropic unbalanced OT | Entropic couplings improve generalization in high dimension |
| Sampler | ODE vs SDE | Modelling response noise improves heterogeneity metrics |
| Gene embedding | Gene Ontology vs protein-language-model features | Which prior knowledge drives unseen-gene generalization |
| Null anchoring | With vs without $$\varnothing$$ groups | Anchoring lowers the noise floor and false-positive effects |

## 10. Go-to-market and honest risk assessment

*Start with academic and biotech labs that already run single-cell screens, sell them fewer and better
experiments, and turn every partner experiment into training data; the moat is a closed loop of model-chosen
experiments, not the mathematics.*

**Who buys, in order.**

1. **Academic and biotech labs running perturbation screens.** Unpaid design partners first. They have data, pain
   (screens are expensive) and the patience to test a new tool. Pune and Mumbai have strong research institutes
   nearby (IISER Pune and NCL Pune, for example) that make natural first conversations.
2. **Oncology teams at biotech and pharma.** Pay for combination synergy maps and resistance maps
   ([Sections 6.5–6.6](#65-combinations-and-a-transcriptome-wide-bliss-synergy-score)), where the unbalanced,
   viability-aware model is a genuine differentiator.
3. **Screening service providers and CROs as a channel.** Bundle an "in-silico pre-screen" with their wet-lab
   service: Alqeora picks the experiments, they run them, and both sides benefit from a cheaper, smarter screen.

**Business model.** Begin with per-project analyses (a predicted screen plus an experiment-ranking report). Move
to a platform subscription once results are reproducible. The long-term asset is the data flywheel: every partner
experiment that Alqeora selected and the partner ran becomes proprietary training data in exactly the regions
where the model was most uncertain.

**Competition.** Well-funded companies (Recursion, insitro, Xaira, Tahoe Therapeutics, Noetik and others),
large-pharma internal teams, nonprofit efforts such as the Arc Institute's virtual-cell work, and open-source
academic models. Do not compete on scale. Compete on the things they under-serve: viability-aware unbalanced
modelling, combinations, calibrated uncertainty, and benchmarks honest enough that a skeptical biologist trusts
them.

**Key risks and mitigations.**

| Risk | Why it matters | Mitigation |
| --- | --- | --- |
| Simple baselines win | The field's own benchmarks show this happens often | Pre-registered comparison against the hardest baselines; lead with metrics they cannot address (viability, heterogeneity, combinations) |
| No biology co-founder | Biologists will not trust a tool built without domain judgment | Recruit a computational-biology co-founder or advisor before pitching anyone |
| No wet-lab validation | In-silico gains are claims until experiments confirm them | One design partner runs a small prospective test of model-chosen vs randomly chosen experiments |
| Batch effects and data quality | Can create apparent effects that are artefacts | Within-plate coupling, null-perturbation anchoring and leakage monitors ([Section 8](#8-diagnostics)) |
| Big players release open models | Erodes any pure-model advantage | Moat in the closed loop and partner data, not in weights |
| Mass estimates are noisy | Viability from cell counts is a proxy | Report viability-model results separately, with calibration plots |

**Honest verdict.** The buyer spends heavily on the exact problem, the mathematics is necessary rather than
decorative, and bio-AI attracts capital readily. It is still a hard company to build, because credibility in drug
discovery is earned with wet-lab results. So Alqeora uses a pre-committed kill-or-continue rule: continue as a
company only if a design partner runs model-chosen experiments prospectively and they beat randomly chosen ones.
Either way, the preprint and open-source repository stand on their own as research contributions.
