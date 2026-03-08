+++
date = "2026-03-04"
draft = false
title = "基础原理"
status = "unfinished"
+++

{{< math >}}

# Diffusion Policy 与 Flow Matching：基础原理

本文总结 **Diffusion Models、Diffusion Policy 和 Flow Matching** 的理论基础，同时强调公式推导与直觉理解。

核心问题是：

> 模型如何学习把一个简单的噪声分布转换为复杂的数据分布？

这个问题广泛出现在：

- 图像生成
- 视频生成
- 以及越来越多的 **机器人策略学习（robot policy learning）**

---

# 1. 基础概念

在理解 diffusion 和 flow matching 之前，需要一些基础概念。

---

## KL 散度

KL 散度用于衡量两个概率分布之间的差异。

$$
KL(q(x)\|p(x)) = \int q(x)\log\frac{q(x)}{p(x)}dx
$$

期望形式：

$$
KL(q\|p) = \mathbb{E}_{q(x)}[\log q(x) - \log p(x)]
$$

### 直觉理解

- KL 散度衡量 **p 对 q 的近似程度**
- KL 始终 **≥ 0**
- 当两个分布完全一致时 KL = 0

在生成模型中，KL 散度通常用于：

- 让模型分布接近 **真实后验分布**
- 或接近 **简单先验（如高斯分布）**

---

## 最大似然估计

最大似然估计（MLE）通过最大化

$$
\log p_\theta(x)
$$

来学习模型参数。

这等价于最小化 **负对数似然（negative log likelihood）**。

对于生成模型来说这是理想目标，但对于带 latent variable 的模型通常难以直接计算。

---

## 变分推断与 ELBO

假设我们有 latent variable model：

$$
p_\theta(x) = \int p_\theta(x|z)p(z)dz
$$

该积分通常不可计算，因此引入近似后验：

$$
q_\phi(z|x)
$$

可以得到：

$$
\log p_\theta(x) = ELBO + KL(q_\phi(z|x)\|p_\theta(z|x))
$$

其中

$$
ELBO = \mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)] - KL(q_\phi(z|x)\|p(z))
$$

由于 KL ≥ 0：

$$
\log p_\theta(x) \ge ELBO
$$

因此训练目标可以转化为：

**最大化 ELBO**。

### ELBO 的两个部分

#### 重建项

$$
\mathbb{E}_{q_\phi(z|x)}[\log p_\theta(x|z)]
$$

保证 decoder 能够从 latent 重建输入数据。

#### KL 正则项

$$
KL(q_\phi(z|x)\|p(z))
$$

约束 latent distribution 接近先验分布。

### 为什么需要变分推断

原始目标是：

$$
q_\phi(z|x) \approx p_\theta(z|x)
$$

但真实后验为：

$$
p_\theta(z|x) = \frac{p_\theta(x|z)p(z)}{p_\theta(x)}
$$

其中

$$
p_\theta(x) = \int p_\theta(x|z)p(z)dz
$$

难以直接计算，因此使用 ELBO 进行优化。

---

## 重参数技巧

如果

$$
z \sim \mathcal{N}(\mu,\sigma^2)
$$

可以改写为

$$
z = \mu + \sigma\epsilon
$$

其中

$$
\epsilon \sim \mathcal{N}(0,1)
$$

这样可以使采样过程可微，从而允许梯度传播。

---

## U-Net

U-Net 是 diffusion 模型中常见的网络结构。

主要特点：

- encoder-decoder 结构
- skip connection
- 多尺度特征融合

输入通常包括：

- 带噪声的数据
- timestep
- 条件信息（例如文本 embedding）

输出通常为：

- 噪声预测
- 或 velocity / score 等目标

---

# 2. Diffusion Models

Diffusion 模型通过 **反转一个逐渐加噪的过程** 来生成数据。

包含两个过程：

- forward diffusion（逐渐加噪）
- reverse diffusion（逐渐去噪）

核心思想：

真实数据分布非常复杂，而 **高斯分布非常简单**。

因此模型学习：

**如何从高斯噪声逐渐恢复出真实数据。**

---

# 3. Forward Diffusion Process

Forward process 会逐步向数据加入高斯噪声。

$$
x_t = \sqrt{\bar{\alpha}_t}x_0 + \sqrt{1-\bar{\alpha}_t}\,\epsilon
$$

其中

$$
\epsilon \sim \mathcal{N}(0,I)
$$

并且

$$
\bar{\alpha}_t = \prod_{s=1}^t \alpha_s
$$

### 直觉

- $\sqrt{\bar{\alpha}_t}$ 控制保留原始信号
- $\sqrt{1-\bar{\alpha}_t}$ 控制噪声比例

随着时间步增加：

$$
x_T \approx \mathcal{N}(0,I)
$$

最终样本会变成 **纯高斯噪声**。

### 为什么会变成高斯

不断加入独立高斯噪声会使分布逐渐被噪声主导。

直觉上：

原始信息逐渐被抹去，最终只剩随机噪声。

### 为什么这是一个分布过程

Forward process 不是确定性函数。

给定同一个 $x_0$：

不同噪声会产生不同 $x_t$。

因此输出是 **随机变量**。

---

# 4. Reverse Diffusion Process

Reverse process 学习逐步去除噪声。

通常训练一个网络：

$$
\epsilon_\theta(x_t,t)
$$

预测噪声。

损失函数：

$$
L = \mathbb{E}\left[\|\epsilon - \epsilon_\theta(x_t,t)\|^2\right]
$$

即 **MSE loss**。

### 为什么预测噪声

从变分推导可以得到训练目标可以分解为：

- 常数项
- 去噪项
- 重建项

其中最重要的是 **去噪项**。

由于 forward process 是高斯分布，reverse conditional 也可以写成高斯形式。

因此问题可以转化为：

**估计均值 → 预测噪声**。

## Sampling

生成过程：

1. 从高斯噪声开始

$$
x_T \sim \mathcal{N}(0,I)
$$

2. 逐步去噪

$$
x_T \rightarrow x_{T-1} \rightarrow ... \rightarrow x_0
$$

---

# 5. Diffusion Model Architecture

典型 diffusion 模型包含三个模块。

---

## Text Encoder

将文本 prompt 转换为 embedding。

常见模型：

- CLIP
- BERT
- GPT
- T5

强大的 text encoder 通常会显著提升生成质量。

---

## Generation Model

核心网络通常是：

**U-Net**

输入：

- noisy sample
- timestep
- text embedding

输出：

- predicted noise

---

## Decoder

如果 diffusion 在 latent space 进行，需要 decoder 还原数据。

```text
image → encoder → latent
latent → diffusion
latent → decoder → image
```

---

# 6. Evaluation Metrics

## FID Score

Fréchet Inception Distance 用于比较生成图像和真实图像的分布差异。

流程：

1. 生成大量图像
2. 使用 CNN 提取特征
3. 拟合为高斯分布
4. 计算 Fréchet distance

FID 越低越好。

---

## CLIP Score

CLIP score 衡量图像和文本之间的语义匹配程度。

CLIP 将：

- 图像
- 文本

映射到同一 embedding 空间。

---

# 7. Diffusion Policy

Diffusion 模型也可以生成 **机器人动作序列**。

在 Diffusion Policy 中：

forward process：给动作加入高斯噪声

reverse process：恢复动作序列。

优点：

- 可以表示多模态策略
- 生成平滑轨迹
- 适合 manipulation 任务

---

# 8. Flow Matching

Flow Matching 是 diffusion 的连续时间版本。

它学习：

**从噪声到数据的连续向量场。**

---

## 核心思想

$$
x_0 \sim p_{data}
$$

$$
x_1 \sim \mathcal{N}(0,I)
$$

定义路径：

$$
x_t = (1-t)x_0 + tx_1
$$

学习 velocity field：

$$
v_\theta(x_t,t)
$$

满足：

$$
\frac{dx_t}{dt} = v_\theta(x_t,t)
$$

训练目标：

$$
\mathbb{E}\|v_\theta(x_t,t) - v^*(x_t,t)\|^2
$$

## 直觉

Diffusion：

```text
noise → many denoising steps → data
```

Flow Matching：

```text
noise → continuous flow → data
```

## 优势

- 更少采样步数
- 连续时间建模
- 训练目标简单
- 生成更快

因此广泛用于：

- 图像生成
- 视频生成
- 机器人控制

---

# 9. Personal Understanding

Diffusion 和 Flow Matching 本质上解决同一个问题：

**从简单分布生成复杂分布。**

Diffusion：

```text
noise → many denoising steps → data
```

Flow Matching：

```text
noise → continuous flow field → data
```

在机器人领域，这类模型可以生成 **平滑动作轨迹**，非常适合 manipulation 和连续控制任务。
