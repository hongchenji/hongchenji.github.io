+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'Pali-X'
status = "unfinished"
+++

**Pali-X (VLM only, but backbone for RT-2)**

_by Google research_

PaLI-X is a multilingual vision and language model with reusable scaled-up components (from PaLI), consisting of a pretrained large-capacity visual encoder (using ViT 22B as the starting point) and a pretrained language-only encoder-decoder (using UL2: Unifying language learning paradigms as the starting point, 32 B), further trained at-scale on a vision-and-language data mixture using a combination of self-supervision and full-supervision signals.

**Architecture**

1. The images are processed by a ViT encoder, resulting in visual embedding which is flattened and projected using MLP
2. Image embeddings along with embedding from the text are fed into the PaLI encoder-decoder backbone (which is a UL2 model of 32 B)
3. This leads to PaLi-X being 22B+32B = 55B model
4. PaLI-X is basically PaLI with the components scaled up

**Results**

Results are not that interesting, seems like the scaling had a very little improvement over the old PaLI model
