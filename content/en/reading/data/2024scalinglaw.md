+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'Scaling Law'
status = "unfinished"
+++

**Data Scaling Laws in Imitation Learning for Robotic Manipulation**

Motivation: In NLP & CV data scaling laws have already been established. This work conducted an empirical study on data scaling (focused on policies for robotic manipulation).

Method:

- They explore environment generalization and object generalization for single-task policies (thus no task-level generalization). Unlike OXE, they focus on training a policy that can be applied to unseen environments & objects (within same object category) without fine-tuning.
- Reporting success rate + normalized score (based on task steps acceptance criteria). Empirical study on two tasks (+ verification of data collection strategy on two further tasks) → based on 40,000 demonstrations, 15,000 roll-outs
- based on data collection using UMI + Diffusion Policy (DinoV2, temporal ensemble from ACT)

Results:

- object generalization: more objects → consistent performance improvements + less demonstrations per object needed
- environment generalization: more environments improve generalization even with constant number of demonstration. For small number of environments, performance gain for increasing number of objects is bigger than when using more environments
- **increasing diversity by simultaneously changing environment and objects is more efficient than merely increasing number of demonstrations for each object/environment individually**
- **power-law exists between generalization capabilities and number of objects, number of environments**, or environment-object pairs BUT NOT with number of demonstrations → could be used for predicting for larger-scale data
- recommendations for data collection strategy (for similar task complexity)
  - -> **with increasing number of environments, collecting data of different objects per environment becomes less important (one object per environment recommended)**
