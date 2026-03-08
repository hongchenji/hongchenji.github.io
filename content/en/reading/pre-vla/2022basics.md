+++
date = "2026-03-04"
draft = false
title = "Basics"
status = "unfinished"
+++

{{< math >}}

# Diffusion Policy and Flow Matching: Foundations

This note summarizes the foundations of **diffusion models**, **diffusion policy**, and **flow matching**, with an emphasis on both the formal derivation and the intuition behind them.

The central question is:

> How can a model learn to transform a simple noise distribution into a complex structured data distribution?

This question appears in image generation, video generation, and increasingly in **robot policy learning**.

---

# 1. Background Concepts

Before discussing diffusion and flow matching, a few background ideas are useful.

## KL Divergence

KL divergence measures the difference between two probability distributions.

$$
KL(q(x)\|p(x)) = \int q(x)\log\frac{q(x)}{p(x)}dx
$$

In expectation form:

$$
KL(q\|p) = \mathbb{E}_{q(x)}[\log q(x) - \log p(x)]
$$

### Intuition

- KL divergence measures how well distribution **p** approximates **q**
- KL is always non-negative
- KL is zero only when the two distributions are identical

In many generative models, KL divergence appears because we want a learned distribution to match either:

- the true posterior, or
- a simple prior such as a Gaussian

---

## Maximum Likelihood

Maximum likelihood estimation (MLE) learns parameters by maximizing

$$
\log p_\theta(x)
$$

Equivalently, it minimizes the negative log-likelihood.

This is the ideal objective for generative modeling, but for latent-variable models it is often hard to compute directly.

---

## Variational Inference and ELBO

Suppose we have a latent-variable model

$$
p_\theta(x) = \int p_\theta(x|z)p(z)\,dz
$$

The integral is often intractable, so we introduce an approximate posterior

$$
q_\phi(z|x)
$$

Then the log-likelihood can be decomposed as

$$
\log p_\theta(x)
= ELBO + KL(q_\phi(z|x)\|p_\theta(z|x))
$$

where

$$
ELBO = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - KL(q_\phi(z|x)\|p(z))
$$

Since KL divergence is non-negative

$$
\log p_\theta(x) \ge ELBO
$$

So instead of directly maximizing $\log p_\theta(x)$ we maximize the **ELBO**.

### ELBO components

$$
ELBO = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - KL(q_\phi(z|x)\|p(z))
$$

This has two parts.

**1. Reconstruction term**

$$
\mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)]
$$

This encourages the decoder to reconstruct $x$ well from $z$.

**2. KL regularization term**

$$
KL(q_\phi(z|x)\|p(z))
$$

This pushes the latent distribution toward the prior.

### Why variational inference appears

The original goal is to match

$$
q_\phi(z|x) \approx p_\theta(z|x)
$$

But the true posterior

$$
p_\theta(z|x)=\frac{p_\theta(x|z)p(z)}{p_\theta(x)}
$$

depends on

$$
p_\theta(x)=\int p_\theta(x|z)p(z)\,dz
$$

which is hard to compute. So we optimize the ELBO instead.

---

## Reparameterization Trick

To sample from

$$
z \sim \mathcal{N}(\mu,\sigma^2)
$$

we rewrite it as

$$
z = \mu + \sigma\epsilon, \qquad \epsilon \sim \mathcal{N}(0,1)
$$

This separates the randomness from the learnable parameters and allows gradients to flow through sampling.

---

## U-Net

A U-Net is a common neural architecture used in diffusion models.

Its main features are

- a downsampling path for extracting coarse features
- an upsampling path for reconstructing details
- skip connections between matching resolution levels

In diffusion models, the U-Net receives

- a noisy input
- a timestep
- optionally a conditioning signal such as text

and predicts either

- the noise
- the clean signal
- or a velocity-like target depending on the formulation

---

# 2. Diffusion Models

A diffusion model learns to generate data by reversing a gradual noising process.

There are two processes

- **forward diffusion**: gradually destroy structure by adding Gaussian noise
- **reverse diffusion**: learn how to reconstruct structure from noise

The key idea is that while the data distribution may be complicated, a standard Gaussian is simple. Instead of modeling the data distribution directly, we learn how to move from Gaussian noise back to data.

---

# 3. Forward Diffusion Process

The forward process gradually adds independent Gaussian noise to a data sample.

At timestep $t$

$$
x_t = \sqrt{\bar{\alpha}_t}x_0 + \sqrt{1-\bar{\alpha}_t}\,\epsilon
$$

where

$$
\epsilon \sim \mathcal{N}(0,I)
$$

and

$$
\bar{\alpha}_t = \prod_{s=1}^t \alpha_s
$$

with $\alpha_t = 1-\beta_t$.

### Intuition

- $\sqrt{\bar{\alpha}_t}$ preserves the original signal
- $\sqrt{1-\bar{\alpha}_t}$ controls the noise level
- as $t$ increases the signal is gradually destroyed

For sufficiently large $T$

$$
x_T \approx \mathcal{N}(0,I)
$$

regardless of the original sample.

### Why this happens

Repeatedly adding independent Gaussian noise drives the distribution toward a Gaussian. Intuitively the original information is gradually washed out and the result becomes dominated by noise.

### Why the process is stochastic

Given the same $x_0$, sampling different noise values produces different $x_t$. Therefore the forward process defines a **distribution over states**, not a deterministic mapping.

---

# 4. Reverse Diffusion Process

The reverse process learns how to remove noise.

Instead of predicting $x_{t-1}$ directly we train a network

$$
\epsilon_\theta(x_t,t)
$$

to predict the noise that was added.

The training loss is

$$
L = \mathbb{E}_{x_0,\epsilon,t}\left[\|\epsilon - \epsilon_\theta(x_t,t)\|^2\right]
$$

which is simply mean squared error between the true noise and the predicted noise.

### Why predicting noise works

The variational objective of diffusion can be decomposed into several terms. In practice the dominant learning signal comes from the denoising term. Because the forward process is Gaussian, the reverse conditional distribution can also be written in Gaussian form. Estimating its mean becomes equivalent to predicting the noise.

### Sampling

Generation proceeds as follows.

1. Sample Gaussian noise

$$
x_T \sim \mathcal{N}(0,I)
$$

2. Apply the reverse process iteratively

$$
x_T \rightarrow x_{T-1} \rightarrow \dots \rightarrow x_0
$$

---

# 5. Diffusion Model Architecture

A text-conditioned diffusion system often contains three components.

## Text Encoder

The text encoder converts prompts into embeddings. Typical choices include

- CLIP
- BERT
- GPT-style encoders
- T5

A strong text encoder usually improves semantic alignment and generation quality.

## Generation Model

The core generation network is typically a **U-Net**.

Inputs

- noisy sample
- timestep
- text embedding

Output

- predicted noise (or an equivalent target)

## Decoder

If diffusion operates in latent space a decoder converts latent representations back to the final output.

```
image → encoder → latent
latent → diffusion
latent → decoder → image
```

---

# 6. Evaluation Metrics

## FID Score

Fréchet Inception Distance measures the difference between generated images and real images.

Typical procedure

1. generate many images
2. extract features using a CNN
3. approximate both feature distributions as Gaussians
4. compute the Fréchet distance

Lower FID indicates better generation quality.

## CLIP Score

CLIP score measures semantic alignment between text and image by comparing their embeddings in a shared representation space.

---

# 7. Diffusion Policy in Robotics

Diffusion models can generate robot action trajectories instead of images.

In **Diffusion Policy**

- the forward process adds Gaussian noise to action sequences
- the reverse process reconstructs the clean actions

This formulation is useful because robot policies are often multimodal and temporally smooth. Diffusion models naturally represent multiple valid behaviors and produce smooth trajectories.

---

# 8. Flow Matching

Flow Matching is a continuous-time formulation of generative modeling.

Instead of learning discrete denoising steps it learns a **vector field** that continuously transports samples from noise to data.

## Core idea

Let

$$
x_0 \sim p_{data}
$$

and

$$
x_1 \sim \mathcal{N}(0,I)
$$

Define an interpolation path

$$
x_t = (1-t)x_0 + tx_1
$$

We learn a velocity field

$$
v_\theta(x_t,t)
$$

such that

$$
\frac{dx_t}{dt} = v_\theta(x_t,t)
$$

The training objective is

$$
\mathbb{E}\|v_\theta(x_t,t) - v^*(x_t,t)\|^2
$$

## Intuition

Diffusion

```
noise → many denoising steps → data
```

Flow matching

```
noise → continuous flow → data
```

## Advantages

Flow matching can offer

- fewer sampling steps
- continuous-time modeling
- simpler regression targets
- faster generation

---

# 9. Personal Understanding

Both diffusion and flow matching address the same fundamental problem: transforming a simple noise distribution into a complex data distribution.

Diffusion can be viewed as

```
noise → iterative denoising → data
```

Flow matching can be viewed as

```
noise → continuous vector field → data
```

For robotics both approaches are attractive because they can generate **smooth action trajectories**, which are essential for manipulation and continuous control.
