---
title: "Reading"
description: "Reading lists & paper notes on robot foundation models."
---

This page organizes notes on **robot foundation models** and related systems.

Instead of listing papers randomly, I try to understand them through two questions:

- how robot policies are represented
- how robot learning systems are built

---

# Robot Policy Evolution

A simplified view of how robot policies have evolved.

| Model            | Key Idea                      |
| ---------------- | ----------------------------- |
| RT-1             | action binning                |
| RT-2             | tokenized action prediction   |
| Diffusion Policy | action diffusion              |
| π0               | flow-based continuous actions |
| π0.5             | hierarchical VLA + flow       |
| FAST             | efficient action tokenization |

---

# Action Representation Methods

Different ways robot actions are represented in foundation models.

- **Binning** – discretizing each action dimension
- **Tokenization** – treating actions like language tokens
- **Diffusion** – generating action sequences through denoising
- **Flow** – continuous action generation through vector fields
- **FAST** – compression-based action tokenization

---

# Sections

## VLA models

Vision-Language-Action models and robot foundation policies.

## Pre-VLA

Early large-scale robot policy models before VLA.

## Data

Robot datasets used for training large policies.

## Benchmark

Evaluation methods and simulation benchmarks.

## LLM/VLM

Vision-language models used as components of robot systems.

## Tricks

Engineering techniques for training large robot models.

---

## Recent updates

Recent posts from the site are listed below.
