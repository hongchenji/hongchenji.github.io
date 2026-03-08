+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'Dex-UMI'
status = "unfinished"
+++

**DexUMI: Using Human Hand as the Universal Manipulation Interface for Dexterous Manipulation**

Issue: transferring skills from demonstrations with human hands to robotics hands is a big challenge due to embodiment gaps (different kinematic structure, contact surface shape, tactile information, visual appearance) and the diversity of robotic hands on the market

**Method:**

Framework to reduce embodiment gap by

1. **wearable hand exo skeleton**
   1. sharing same joint-to-fingertip position mapping and limits to transfer actions between exoskeleton and robot hand
      1. prioritizes kinematics of fingertip links over links less likely to contact objects (allows greater flexibility)
      2. works with under-actuated and fully-actuated hands but **requires hardware tuning, i.e. for each hand a new exoskeleton needs to be made, they just present a framework on how to make them)!**
   2. trains simple regression model for non-linear mapping between joint encoders and hand motors of exoskeleton
   3. sensors: uses iPhone (during data collection) to obtain wrist pose, uses wide-angle camera under wrist (same as on robot hand), incorporates tactile sensors
   4. ensure good wearability
2. **data processing pipeline** to bridge visual gap
   1. use SAM2 to segment hand (prompt points are always the same as they use initial hand gesture) then inpaint image background (pro painter)
   2. replay recorded joint actions on hand (not robot) to obtain image of hand pose, segment using Sam2 (future work: train image generation model instead to get rid of hardware requirement)
   3. compose images in occlusion-aware manner by using mask to selectively replace pixels

**Results:**

- tested on two robotic hands (inspire hand, XHand)
- relative finger trajectory leads to better success rate + more reliable contacts than absolute (wrist pose is always relative)
- tactile information if noisy makes results worse (better performance with relative fingers again). Exception: if there is a clear, strong sensor reading (e.g. entering bowl with salt), little noise and little visual information, then tactile can help
- 3.2 faster during data collection than teleoperation and reliable success rates

---

**Related Content**

TBD: to which page to move this depending on what other papers (that do no introduce action models directly) we want to document
