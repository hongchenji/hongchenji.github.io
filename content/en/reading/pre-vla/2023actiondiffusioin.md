+++
date = "2026-03-04"
draft = true
title = "Action Diffusion"
status = "unfinished"
+++

# Diffusion Policy: Visuomotor Policy Learning via Action Diffusion

**Authors**
Cheng Chi, Zhenjia Xu, Siyuan Feng, Eric Cousineau, Yilun Du, Benjamin Burchfiel, Russ Tedrake, Shuran Song

**Conference**
Robotics: Science and Systems (RSS)

**Year**
2023

**Paper**
[https://diffusion-policy.cs.columbia.edu/diffusion_policy_2023.pdf](https://diffusion-policy.cs.columbia.edu/diffusion_policy_2023.pdf)

---

# Problem

Robot policies in manipulation tasks must map observations (images, robot states, etc.) to continuous actions.

However, real robot tasks often exhibit several difficult properties:

- **Multi-modal action distributions**: there may be multiple valid ways to complete the same task.
- **Temporal correlations**: actions across time must form coherent trajectories.
- **Precision requirements**: manipulation tasks require smooth and stable control.

Traditional policy learning methods such as behavioral cloning often use **direct regression** to predict actions.

This creates several issues:

- regression tends to average multiple valid behaviors
- policies may produce unstable or inconsistent trajectories
- long-horizon dependencies are difficult to model

As a result, learned policies may perform poorly in complex manipulation tasks.

---

# Why it matters

A good robot policy representation should be able to:

1. represent **multi-modal action distributions**
2. generate **temporally coherent action sequences**
3. produce **smooth continuous trajectories**

Recent generative models such as **diffusion models** have shown strong ability to represent complex distributions in image generation.

This raises an interesting question:

> Can diffusion models be used to represent **robot action policies** instead of images?

If successful, this could allow robot policies to model complex action distributions more naturally than standard regression approaches.

---

# Attempt

Diffusion Policy proposes to model robot control as a **conditional diffusion process over action sequences**.

Instead of generating images, the diffusion model generates **future robot actions** conditioned on observations.

Formally, the model learns the conditional distribution:

p(A_t | O_t)

where:

- O_t : observation history
- A_t : future action sequence

The diffusion model iteratively denoises a noisy action trajectory until it becomes a valid action sequence.

This reframes policy learning as **conditional trajectory generation**.

---

# Model

## Receding Horizon Action Generation

At time step t:

- the policy receives the most recent **T_o observations**
- the model predicts **T_p future actions**
- only the first **T_a actions** are executed

After execution, the system replans using the next observation.

This creates a **receding-horizon control loop**.

---

## Conditional Diffusion

The diffusion model operates over action trajectories.

Training involves:

1. adding Gaussian noise to action sequences
2. training a neural network to predict the noise
3. iteratively denoising during inference

The model conditions the denoising process on observation inputs (images + robot state).

---

## Policy Representation

Instead of learning the joint distribution p(A_t, O_t), the model directly learns:

p(A_t | O_t)

This simplifies inference and focuses the model on action generation conditioned on observations.

---

# Failure / Limitation

Despite strong performance, diffusion policies also introduce some trade-offs.

### Sampling latency

Diffusion models require **multiple denoising steps** during inference.

This can increase computation time compared with single-step regression policies.

---

### Data requirements

Training diffusion policies may require larger datasets compared with simpler imitation learning approaches.

---

### Control frequency constraints

For high-frequency robot control, repeated diffusion sampling may introduce latency challenges.

---

# Results

The authors evaluate Diffusion Policy on several manipulation benchmarks:

- Push-T
- Franka Kitchen
- Robomimic tasks

Results show that diffusion policies outperform several baselines, including:

- Behavioral Cloning
- Transformer-based policies

The improvements are particularly strong on **complex manipulation tasks** with multi-modal action distributions.

---

# Key Idea

The main conceptual contribution of the paper is:

> representing robot policies as **generative models over action trajectories**.

Instead of directly predicting actions, the model generates trajectories through a diffusion process.

This allows the policy to represent complex and multi-modal action distributions.

---

# Takeaway

Diffusion Policy demonstrates that **generative models can serve as robot policies**.

Key insights include:

- diffusion models can represent multi-modal action distributions
- predicting action sequences improves trajectory stability
- generative modeling provides a new perspective on robot policy learning

This work influenced later approaches that treat robot control as **trajectory generation**, including diffusion-based and flow-based VLA models.
