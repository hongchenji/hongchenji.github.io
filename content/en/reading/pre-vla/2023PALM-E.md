+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'PALM-E'
status = "unfinished"
+++

**PALM-E ( Backbone of RT-2)**

_by Robotics at Google , Google research, Technische Universität Berlin_

PaLM-E is a decoder-only LLM that generates textual completions autoregressively given a prefix or prompt. It combines the power of visual models like ViT with language models like PaLM. It is an embodied LM built by injecting multi-modal information such as images into the embedding space of a pre-trained LLM. The model can also be used on general visual language tasks only.

**Architecture**

The architecture of Palm-E consist of three parts: **encoder**, **projector**, **LLM (Palm)**

1. The encoder model takes multi-model sentences with continuous observation such as images, text, state estimates and other modalities (neural scenes)
2. The encoder for the images is a variant of a ViT transformer:
   1. pretrained 4B parameter ViT-4B
   2. pretrained 22B parameter ViT-22B
   3. ViT token learner architecture (ViT+TL) trained end-to-end
3. The ViT maps images into a number of token embedding , then use a projector to convert into language embedding space.
4. The projector (mainly an MLP) convert states estimation vectors( pose,size,color,..), ViT's image embeddings and text, into language embedding vector ( Vocab size is 256k)
5. The language embedding is passed to the LLM (Palm) transformer decoder
6. The output is textual instructions such as goals or plans with steps
7. They convert these plans/goals to low-level robotic actions using previous work. For each task a specific previous work is use:
   1. “Mobile Manipulation Environment” they use **RT-1**
   2. “Long-horizon planning” they use polices similar to **SayCan**

Size of Palm-E:

1. 8B LLM (Palm) + 4B Vit -> Palm-E-12B
2. 62B LLM (Palm) + 22B Vit -> Palm-E-84B
3. 540B LLM (Palm) + 22B Vit -> Palm-E-562B

**Results**

The paper shows that using PaLM and ViT pretraining together with the **full mixture** of robotics and general visual-language data provides a significant performance increase compared to only training on the **respective in-domain** data. Same result was found by RT-2, which also uses a VLM, maybe this is a property of the VLM models??
