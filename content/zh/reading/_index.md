---
title: "Reading"
description: "机器人基础模型相关论文阅读与笔记"
---

本页面整理的是我关于 **Robot Foundation Models（机器人基础模型）** 的阅读笔记。

与其简单列出论文，我更希望从两个问题来理解这个领域：

- 机器人策略是 **如何表示的**
- 机器人学习系统是 **如何逐步构建的**

---

# 机器人策略演化

下面是一条简化的机器人策略技术路线。

| 模型             | 核心思想                                                  |
| ---------------- | --------------------------------------------------------- |
| RT-1             | action binning（动作分桶离散化）                          |
| RT-2             | tokenized action prediction（动作 token 预测）            |
| Diffusion Policy | action diffusion（动作扩散生成）                          |
| π0               | flow-based continuous actions（基于 flow 的连续动作生成） |
| π0.5             | hierarchical VLA + flow（层级式 VLA + flow 控制）         |
| FAST             | efficient action tokenization（高效动作 tokenization）    |

---

# 动作表示方法

在机器人基础模型中，动作可以通过不同方式进行表示。

主要包括：

**Binning**

将每个动作维度离散化为固定数量的 bins。

**Tokenization**

将机器人动作视为 token，并通过序列预测进行生成。

**Diffusion**

通过逐步去噪生成动作序列。

**Flow**

通过连续向量场生成动作轨迹。

**FAST**

先对动作信号进行压缩，再进行 tokenization。

---

# 内容结构

本页面中的笔记大致分为几个部分。

## VLA models

Vision-Language-Action 模型以及相关机器人基础策略。

---

## Pre-VLA

VLA 出现之前的一些大规模机器人策略模型。

---

## Data

用于训练机器人基础模型的数据集。

---

## Benchmark

机器人学习中的评测方法和仿真 benchmark。

---

## LLM / VLM

机器人系统中使用的语言模型与视觉语言模型。

---

## Tricks

训练机器人基础模型时使用的一些工程技巧与实践经验。

---

下面列出更新的章节。
