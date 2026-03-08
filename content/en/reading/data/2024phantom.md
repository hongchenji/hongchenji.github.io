+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'Phantom'
status = "unfinished"
+++

**Phantom: Training Robots Without Robots Using only Human Videos**

_by Stanford_

Idea: allow zero-shot policy deployments on robots without collecting any robot-specific data. Instead anyone with an **RGB-D** camera can provide data. This is made possible by data editing of the input image where the **human hand is inpainted and replaced by a robot render** using an estimated hand pose.

**Method / Architecture**

Assumptions: height and angle of cameras during inference/test are similar, extrinsics of camera during deployment are known

1. Image Editing: Segment arm and replace it with inpainting → overlay robot rendered using Mujoco for appropriate position and viewpoint
2. Action Labeling: using HaMeR a 3D hand pose is estimated at each frame (keypoints + handmesh) → estimate refined using ICP (handmesh vertices and partial pointcloud generated using segmentation mask (SAM2) and depth information)
   1. EE position: midpoint between index finger and thumb
   2. EE orientation: plane through index finger and thumb keypoints
   3. convert pose from camera frame to robot’s frame using camera extrinsics of setup
3. uses Diffusion Policy
4. during inference real robot still gets an overlay with a virtual robot

**Results**

- compares different data editing approaches: hand inpaint (described above), hand mask (black out arm and overlay robot), red line (see EgoMimic)
  - hand inpaint and hand mask perform well, but hand mask needs 73% longer as additional diffusion model needs to generate hand masks
  - red-line fails
- out-of-distribution performance underwhelming even with hand inpaint (from 80% to 20% succes)
- high-quality inpaint using E2FGVI is best but primitive OpenCV inpainting does remarkably well
- trade-off: ease of collection allows for more data but per-demo human demonstrations are less precise (due to uncertainty of hand pose estimation)
- limitations: only works when robot can use same strategy as human to complete task (prone to environment collisions), occlusions worsen performance, only pinch grasps
