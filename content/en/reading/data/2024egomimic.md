+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'EgoMimic'
status = "unfinished"
+++

**EgoMimic: Scaling Imitation Learning via Egocentric Videos**

Human videos from egocentric perspective have high similarity to robot data and can be used to augment data sets for training robot policies. Human collection typically yields more demonstrations in the same time as teleoperation data. Other works are limited to extract high-level intent from human demonstration while low-level planner is trained only on robot data (e.g. MimicPlay). The idea is to **combine human and robot demonstrations and learn from both sources in unified, equal way**

**Method/Architecture**

- human: data collected with Project Aria glasses: egocentric rgb video, 3d hand tracking, slam
- Robot: data collected with low-cost bi-manual robot, also uses project aria glasses as main camera
- mitigate differences between human and robot data
  - strategies for transferring to unified camera-centered frame as for humans the camera frame changes constantly but is fixed for robot
  - temporal alignment of data sources (slow down human data by factor 4)
  - gaussian normalization is applied to hand and robot data separately as their distributions normally show discrepancies (e.g. due to bio-mechanical differences)
  - bridging visual appearance gap: similar to related work, **hand and robot are masked out, a red line overlay is added to indicate endeffector direction**
    - **SAM2**: specifically sam2.1_hiera_tiny.pt
    - arm and robot are **masked for training and during inference** (run SAM in real-time)
    - red line based on either corners of segmentation box (hand) or hand, elbow, arm joints (robot, using forward kinematics
- co-training with hand and robot data → shared representation/architecture
  - proprioception/hand pose: normalized and passed through linear layer → produces 1 proprioception token
  - undistorted aria (human, robot) images are masked and passed along with robot wrist rgb to Visual encoder (**Resnet18**)
  - style token z, see ACT paper
  - hidden output of transformer is passed to different decoders depending on whether joint or pose actions are generated
  - human data lacks gripper action and wrist cams → additional tokens for cams for robot concatenated as transformer input, gripper is included as additional joint (see joint loss)

**Results**

- in-domain task performance increased, where baselines often miss object by small amount egomimic reaches more precisely
  - co-training better than hierarchical usage of human data
  - egomimic with human data improves performance by 47% over only robot data
- generalization to new environments only with human data possible
