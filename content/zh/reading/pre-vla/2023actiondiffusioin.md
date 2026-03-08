+++
date = "2026-03-04"
draft = true
title = "动作扩散模型"
status = "unfinished"
+++

# Diffusion Policy：通过动作扩散进行视觉运动策略学习

**作者**
Cheng Chi, Zhenjia Xu, Siyuan Feng, Eric Cousineau, Yilun Du, Benjamin Burchfiel, Russ Tedrake, Shuran Song

**会议**
Robotics: Science and Systems (RSS)

**年份**
2023

**论文**
[https://diffusion-policy.cs.columbia.edu/diffusion_policy_2023.pdf](https://diffusion-policy.cs.columbia.edu/diffusion_policy_2023.pdf)

---

# Problem

在机器人操作任务中，策略需要根据观测（图像、机器人状态等）生成连续动作。

但真实机器人任务通常具有几个困难特性：

- **动作分布是多模态的**：同一个任务可能有多种可行解
- **时间相关性很强**：动作必须组成连贯的轨迹
- **控制精度要求高**：操作任务需要稳定且平滑的控制

传统策略学习方法（例如 Behavioral Cloning）通常使用 **直接回归（direct regression）** 来预测动作。

这种方法会带来一些问题：

- 多个可行行为会被平均化
- 生成的动作轨迹不稳定
- 难以建模长时序依赖

因此，在复杂 manipulation 任务中，这类策略往往表现不稳定。

---

# Why it matters

一个好的机器人策略表示方式需要能够：

1. 表达 **多模态动作分布**
2. 生成 **时间上连贯的动作序列**
3. 产生 **平滑的连续轨迹**

近年来 **Diffusion Models** 在图像生成中表现出很强的能力，能够表示复杂分布。

因此一个自然的问题是：

> 是否可以使用 diffusion model 来表示 **机器人策略分布**？

如果可行，这将提供一种比传统回归策略更灵活的方式来建模复杂动作分布。

---

# Attempt

Diffusion Policy 提出了一个新的思路：

**将机器人策略建模为条件扩散过程（conditional diffusion process）。**

与图像生成类似，模型通过扩散过程生成 **未来动作序列**。

模型学习的条件分布为：

p(A_t | O_t)

其中：

- O_t：观测历史（图像 + 状态）
- A_t：未来动作序列

扩散模型从带噪动作序列开始，通过多步去噪逐渐生成有效动作。

因此策略学习被重新表述为：

> **条件动作轨迹生成问题（conditional trajectory generation）。**

---

# Model

## Receding Horizon Control

在时间步 t：

- 策略接收最近 **T_o 个观测**
- 模型预测 **T_p 个未来动作**
- 实际只执行前 **T_a 个动作**

随后系统根据新的观测重新规划。

这种机制形成了一个 **receding horizon control loop**。

---

## Conditional Diffusion

扩散模型作用在 **动作轨迹** 上。

训练过程包括：

1. 对动作序列加入高斯噪声
2. 训练网络预测噪声
3. 在推理阶段逐步去噪

去噪过程以观测信息（图像 + 机器人状态）作为条件。

---

## Policy Representation

模型直接学习条件分布：

p(A_t | O_t)

而不是联合分布 p(A_t, O_t)。

这样可以简化推理过程，并专注于 **在给定观测条件下生成动作**。

---

# Failure / Limitation

尽管 Diffusion Policy 表现良好，但也存在一些权衡。

### 推理延迟

扩散模型在推理阶段需要 **多步去噪**。

相比单步回归策略，这会增加计算时间。

---

### 数据需求

扩散策略通常需要较大的训练数据规模。

---

### 控制频率限制

在高频控制任务中，多步扩散采样可能带来延迟问题。

---

# Results

论文在多个机器人操作 benchmark 上进行了评估：

- Push-T
- Franka Kitchen
- Robomimic 任务

实验结果表明 Diffusion Policy 优于多种 baseline：

- Behavioral Cloning
- Transformer policy

特别是在 **复杂 manipulation 任务** 中表现明显更好。

---

# Key Idea

论文的核心思想是：

> 将机器人策略表示为 **动作轨迹的生成模型（generative model）**。

策略不再直接预测动作，而是通过扩散过程生成动作序列。

这种方式能够自然表示 **复杂和多模态的动作分布**。

---

# Takeaway

Diffusion Policy 展示了一种新的机器人策略建模方式：

- 使用生成模型表示策略
- 通过扩散过程生成动作轨迹
- 更好地处理多模态行为

这项工作也启发了后续许多将机器人控制视为 **轨迹生成问题** 的研究，例如 diffusion-based 或 flow-based VLA 模型。
