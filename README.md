
# 📖 Theory of Deep Adaptive Resonance Differentiable DSP (DAR-DDSP)

---

## 🔹 1. Abstract

**DAR-DDSP** is a framework where a **differentiable DSP plant** is trained under the control of **adaptive resonance learning laws**, extended into deep embedding spaces.

* DSP modules (oscillators, filters, envelopes) provide a **structured, interpretable plant**.
* A neural encoder produces **deep embeddings** of audio features.
* Adaptive Resonance dynamics allocate and stabilize **prototype memories** in embedding space.
* The optimizer couples **error descent** (SGD/Adam) with **resonance regularization** (ART laws), turning the entire plant into a **closed-loop resonant system**.

This yields models that are both **adaptive** (plastic) and **stable** (resonant), ideal for continual timbre learning, style transfer, and long-horizon training.

---

## 🔹 2. System Model

**State:**

$$
x_t = \begin{bmatrix}
\theta_t \\ h_t \\ \alpha_t \\ \mu_t \\ \sigma_t \\ W_t
\end{bmatrix}
$$

* $\theta_t$: DSP parameters (cutoff, Q, envelope times, mix levels).
* $h_t$: optimizer buffers (momentum/Adam states).
* $\alpha_t,\mu_t,\sigma_t$: adaptive hyperparameters (LR, momentum, dither).
* $W_t=\{w_j\}$: set of prototypes in embedding space (deep ART memory).

**Outputs:**

$$
y_t = [\ell_t, r_t], \quad \ell_t=L(\theta_t;\xi_t),\ r_t=\text{reward}
$$

**Embeddings:**

$$
h^*_t = E(\phi(x_t)), \quad \phi = \text{STFT/log-mel feature extractor}, \ E = \text{deep encoder network}
$$

---

## 🔹 3. Learning Laws

### 3.1 Error-driven (gradient)

$$
m_t = \mu_t m_{t-1} + (1-\mu_t) g_t, \quad g_t = \nabla_\theta L(\theta_t)
$$

$$
\theta_{t+1} = \theta_t - \alpha_t m_t + \sigma_t D(\theta_t)\eta_t
$$

### 3.2 Resonance-driven (prototypes in embedding space)

$$
h^*_{t+1} = h^*_t + \lambda (w_j - h^*_t)
$$

$$
w_j \gets w_j + \eta \big((h^*_t \land w_j) - w_j\big)
$$

with vigilance:

$$
M_j = \frac{|h^*_t \land w_j|}{|h^*_t|} \ge \rho
$$

→ update existing prototype, else allocate new one.

### 3.3 Coupling

* **Resonance regularizer**:

$$
\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{task}} + \lambda_{\text{ART}} \|h^*_t - w_j\|^2
$$

* **Feedback correction**:

$$
\theta_{t+1} = \theta_t - \alpha_t m_t + \beta(h^*_{t+1} - h^*_t)
$$

---

## 🔹 4. DSP / Control View

* **Plant**: differentiable DSP chain → structured, interpretable, causal.
* **Deep encoder**: compresses plant’s output into embedding space.
* **Resonance dynamics**: add a **second pole** (like spring–damper) → stable attractors.
* **Vigilance**: nonlinear switching law = allocate new attractors when input is novel.

Thus: **DAR-DDSP = error-driven control + resonance-driven memory inside a differentiable DSP plant.**

---

## 🔹 5. Stability–Plasticity in DSP Context

* **Stability**: prototypes anchor spectral/timbral regimes (e.g. a “bright saw,” “dark square”).
* **Plasticity**: vigilance spawns new prototypes when a new timbral regime appears.
* **Continual adaptation**: plant can learn new styles without overwriting old sounds.

---

## 🔹 6. Design Heuristics

* **Learning rates**:
  $\alpha$ adapted multiplicatively (up if improving, down if noisy).
* **Momentum**: $\mu$ nudged by reward trend.
* **Dither**: $\sigma$ small, raised only on plateaus.
* **Prototype LR**: $\eta \sim 10^{-3}$.
* **Vigilance**: $\rho \in [0.7, 0.9]$.
* **Resonance weight**: $\lambda_{\text{ART}} \ll 1$ in regression tasks.

---

## 🔹 7. Why DAR-DDSP

* **Structured + deep**: combines interpretable DSP with expressive embeddings.
* **Memory-aware optimization**: optimizer itself has attractors in embedding space.
* **Resonant learning law**: 2nd-order system with bounded oscillations, not just gradient smoothing.
* **Stability–plasticity balance**: learn new timbres without catastrophic forgetting.
* **Engineering-ready**: discrete-time recursions, bounded gains, DSP-style stability guarantees.

---

## ✅ Thesis

> **Deep Adaptive Resonance Differentiable DSP (DAR-DDSP)** is a learning framework where a differentiable DSP plant is optimized under Adaptive Resonance Learning Laws.
> By coupling gradient descent with resonance-driven prototypes in deep embedding space, DAR-DDSP yields optimizers that are **resonant, adaptive, and memory-stabilized**.
> This reframes training as a **closed-loop control system** with both error descent and resonance memory, enabling stable continual learning in differentiable audio synthesis.

