+++
date = "2026-03-04"
draft = true
title = "OpenVLA"
status = "unfinished"
+++

# OpenVLA: An Open-Source Vision-Language-Action Model

**Authors:** Moo Jin Kim*, Karl Pertsch*, Siddharth Karamcheti\*, Ted Xiao, Ashwin Balakrishna, Suraj Nair, Rafael Rafailov, Ethan Foster, Grace Lam, Pannag Sanketi, Quan Vuong, Thomas Kollar, Benjamin Burchfiel, Russ Tedrake, Dorsa Sadigh, Sergey Levine, Percy Liang, Chelsea Finn

**Institutions:** Stanford, UC Berkeley, TRI, Google DeepMind, Physical Intelligence, MIT

**Year:** 2024

Paper:  
https://arxiv.org/pdf/2406.09246

---

# 1. Background

OpenVLA is an **open-source Vision-Language-Action (VLA) model** designed for robot learning.

Recent work such as RT-2 demonstrates strong generalization capabilities using large Vision-Language Models (VLMs), but many existing systems have several limitations:

- most models are **closed-source**
- training pipelines are difficult to reproduce
- fine-tuning large VLA models is computationally expensive
- deployment pipelines are not clearly documented

OpenVLA aims to provide a **fully open implementation of VLA models**, enabling easier training, fine-tuning, and deployment for robot policies.

---

# 2. Core Method

The key idea of OpenVLA is to build a **robot policy directly on top of a pretrained Vision-Language Model**.

The model takes:

image observation + language instruction

and predicts

discretized robot actions

Actions are discretized into **256 bins**, allowing robot control to be framed as a **token prediction problem** similar to language modeling.

---

# 3. Model Architecture

OpenVLA is built on top of the **Prismatic-7B Vision-Language Model**.

## 3.1 Prismatic-7B VLM

The architecture includes both visual and language components.

### Visual Encoder

The visual encoder combines two pretrained models:

- **SigLIP**
- **DINOv2**

Key properties:

- approximately **0.6B parameters**
- the input image is processed by both encoders
- the resulting visual features are **concatenated channel-wise**

This improves representation quality by combining complementary visual features.

---

### Projection Layer

The concatenated visual embeddings are passed through a:

- **2-layer projector**

The projector maps visual features into the token space used by the language model.

---

### Language Model

The language backbone is:

- **LLaMA-2 (7B parameters)**

The model is further **fine-tuned on LLaVA-1.5**, which improves visual-language reasoning capabilities.

---

## 3.2 Action Representation

Robot actions are discretized into **256 bins**.

The model directly predicts these bins as output tokens.

This converts robot control into a **sequence generation problem** conditioned on visual observations and language instructions.

---

## 3.3 Training Efficiency Techniques

Several engineering techniques are used to enable efficient training of the large model:

- **AMP (Automatic Mixed Precision)**
- **FlashAttention**
- **FSDP (Fully Sharded Data Parallel)**
- **LoRA fine-tuning**

These techniques reduce memory consumption and improve training efficiency.

---

# 4. Experiments

(OpenVLA paper evaluation summary – to be filled in)

## Datasets / Tasks

- robot manipulation datasets
- real-world robot demonstrations

## Evaluation Setup

- compare OpenVLA with existing VLA models
- evaluate generalization across tasks

## Results

(results section to be expanded)

---

# 5. Contributions

The main contributions of the OpenVLA paper include:

1. An **open-source Vision-Language-Action model**
2. A reproducible training pipeline for VLA models
3. Efficient fine-tuning techniques for large multimodal robot policies
4. A practical framework for deploying VLA models in robotics

---

# 6. Relevance to Robotics

OpenVLA is particularly important for robotics research because:

- it provides the **first fully open implementation of a VLA model**
- researchers can reproduce and extend VLA training pipelines
- it lowers the barrier for training robot foundation models

Potential applications include:

- robot manipulation
- task generalization
- language-conditioned robot control

---

# 7. Personal Notes

## Observations

OpenVLA mainly focuses on **engineering accessibility and openness**, rather than introducing fundamentally new architectural ideas.

The architecture is largely based on existing VLM components, but the key contribution is providing a **complete open training pipeline**.

---

## Interesting Aspects

Several technical design choices are worth noting:

- combining **SigLIP + DINOv2** for visual encoding
- using **LLaMA-2 + LLaVA-1.5** for language-vision reasoning
- discretizing robot actions into **256 bins**
- leveraging efficient training techniques such as **LoRA and FSDP**

These engineering decisions make the model more practical for research and deployment.

---

## Future Directions

Some potential directions for further research include:

- improving spatial reasoning capabilities
- scaling robot datasets
- integrating reasoning mechanisms such as embodied chain-of-thought
