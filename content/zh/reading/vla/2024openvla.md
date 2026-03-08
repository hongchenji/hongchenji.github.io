+++
date = "2026-03-04"
draft = true
title = "OpenVLA"
status = "unfinished"
+++

# OpenVLA：一个开源的 Vision-Language-Action 模型

**作者**：Moo Jin Kim*, Karl Pertsch*, Siddharth Karamcheti\*, Ted Xiao, Ashwin Balakrishna, Suraj Nair, Rafael Rafailov, Ethan Foster, Grace Lam, Pannag Sanketi, Quan Vuong, Thomas Kollar, Benjamin Burchfiel, Russ Tedrake, Dorsa Sadigh, Sergey Levine, Percy Liang, Chelsea Finn

**机构**：Stanford, UC Berkeley, TRI, Google DeepMind, Physical Intelligence, MIT

**年份**：2024

论文：  
https://arxiv.org/pdf/2406.09246

---

# 1. 背景

OpenVLA 是一个 **开源的 Vision-Language-Action（VLA）模型**，用于机器人学习任务。

近年来的工作（例如 RT-2）表明，大规模 **Vision-Language Model（VLM）** 可以显著提升机器人策略的泛化能力。然而这些系统通常存在一些问题：

- 大部分模型 **闭源**
- 训练流程难以复现
- 大规模 VLA 模型 **微调成本高**
- 实际部署流程缺乏公开实现

OpenVLA 的目标是提供一个 **完全开源的 VLA 实现框架**，从而使研究者能够更容易地训练、微调以及部署机器人策略模型。

---

# 2. 核心方法

OpenVLA 的核心思想是：

**在一个预训练的 Vision-Language Model 上直接构建机器人策略。**

模型输入：图像观测 + 语言指令

输出：离散化的机器人动作

机器人动作被 **离散化为 256 个 bin**。

因此机器人控制问题被转换为一种 **类似语言模型的 token 预测问题**。

---

# 3. 模型结构

OpenVLA 基于 **Prismatic-7B Vision-Language Model** 构建。

## 3.1 Prismatic-7B VLM

模型由视觉模块和语言模块组成。

### 视觉编码器

视觉编码器结合了两个预训练视觉模型：

- **SigLIP**
- **DINOv2**

主要特点：

- 约 **0.6B 参数**
- 输入图像同时经过两个 encoder
- 最终特征 **按 channel 维拼接（concatenate channel-wise）**

这种设计可以融合不同视觉模型的表示能力。

---

### 投影层

拼接后的视觉特征通过：

**2 层 projector**

该 projector 将视觉特征映射到语言模型的 token embedding 空间。

---

### 语言模型

语言模型 backbone 使用：

**LLaMA-2（7B 参数）**

随后模型在 **LLaVA-1.5** 上进一步微调，以增强视觉-语言推理能力。

---

## 3.2 动作表示

机器人动作被离散化为 **256 个 bin**。

模型直接预测这些 bin。

因此机器人控制可以表示为：

**在视觉观测和语言指令条件下的序列生成问题**。

---

## 3.3 训练效率优化

为了能够高效训练大规模模型，OpenVLA 使用了多种工程优化技术：

- **AMP（Automatic Mixed Precision）**
- **FlashAttention**
- **FSDP（Fully Sharded Data Parallel）**
- **LoRA 微调**

这些技术可以：

- 降低显存占用
- 提高训练效率
- 使大模型训练更加可行

---

# 4. 实验

（OpenVLA 论文实验部分待补充）

## 数据集 / 任务

- 机器人操作数据集
- 真实机器人演示数据

## 实验设置

- 与现有 VLA 模型进行比较
- 评估跨任务泛化能力

## 实验结果

（结果部分待补充）

---

# 5. 论文贡献

OpenVLA 的主要贡献包括：

1. 提供一个 **开源的 Vision-Language-Action 模型**
2. 构建可复现的 **VLA 模型训练流程**
3. 提供大规模多模态机器人策略的 **高效微调方法**
4. 提供机器人 VLA 模型的 **实际部署框架**

---

# 6. 与机器人研究的关系

OpenVLA 对机器人研究的重要意义在于：

- 提供 **首个完整开源的 VLA 模型实现**
- 研究者可以复现并扩展 VLA 训练流程
- 降低机器人基础模型研究的门槛

潜在应用包括：

- 机器人操作任务
- 任务泛化
- 语言条件机器人控制

---

# 7. 个人笔记

## 一些观察

OpenVLA 的主要贡献更多体现在 **工程实现与开放性** 上，而不是提出全新的模型结构。

整体架构仍然基于现有的 VLM 组件，但提供了一个 **完整可复现的训练 pipeline**。

---

## 有意思的技术设计

OpenVLA 中几个值得注意的技术选择包括：

- 使用 **SigLIP + DINOv2** 作为视觉编码器
- 使用 **LLaMA-2 + LLaVA-1.5** 作为视觉语言模型
- 将机器人动作 **离散化为 256 个 bin**
- 使用 **LoRA 和 FSDP** 进行高效训练

这些工程设计使模型更加实用，也更容易被研究社区复现。

---

## 未来可能的方向

未来的研究方向可能包括：

- 提升模型的 **空间推理能力**
- 扩展机器人数据规模
- 结合 **Embodied Chain-of-Thought 等推理机制**
