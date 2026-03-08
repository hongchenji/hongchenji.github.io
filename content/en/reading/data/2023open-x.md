+++
date = '2026-03-04T22:26:49+01:00'
draft = true
title = 'Open-X'
status = "unfinished"
+++

**Open-X**

_by collaborative effort by many labs around the world_

**Dataset**

This is the most community-centered release yet: • open dataset (34 research labs, 22 embodiments, 1M+ trajectories, 500+skills) that combines 60 datasets and is released in an easily accessible common format (+colabs). The paper also present a high-capacity model trained on this data, which we call RT-X, exhibits positive transfer and improves the capabilities of multiple robots by leveraging experience from other platforms.

https://docs.google.com/spreadsheets/d/1rPBD77tk60AEIGZrGSODwyyzs5FgCU9Uz3h-3_t2A9g/edit#gid=0

find and filter dataset easily

**Results**

- The models were evaluated in 5 different locations, on different robots performing different tasks.
- Each lab tested it in a head-to-head comparison against the best control system they had developed independently for their own robot.
- The single unified model provided improved performance over each laboratory’s own best method, succeeding at the tasks about 50 percent more often on average.

RT-1-X model is on avg 50% better than the baselines , RT-2-X is 3x better than the prev best RT-2 model

The paper has number of limitations:

1. It does not consider robots with very different sensing and actuation modalities,
2. It does not study generalization to new robots
3. It does not provide a decision criterion for when positive transfer does or does not happen.
