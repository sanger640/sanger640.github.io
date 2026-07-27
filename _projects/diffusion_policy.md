---
layout: page
title: VR Teleop Data Collection for Franka Panda
description: Meta Quest 3 Pro teleoperation for a real Franka Panda, built to collect the demonstrations that train downstream manipulation policies
permalink: /projects/panda-express/
img: assets/img/diff_policy_setup.jpg
importance: 1
category: research
github: https://github.com/sanger640/panda_express
---

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/diff_policy_setup.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        The Franka Panda cell: wrist + external RealSense D435 cameras, a fixed workspace, and the objects used for demonstrations.
    </div>
</div>

## What it is

This is the data-collection backbone behind my [Diffusion Policy](/projects/diffusion-policy/) and [Latent Safety Filters](/projects/latent-safety-filters/) work: a VR teleoperation system for a real Franka Panda arm, built to make collecting good demonstrations fast enough that training a policy on real (not simulated) data is actually practical.

## How it works

- **Teleoperation**: a Meta Quest 3 Pro controller drives the arm in real time — controller pose maps to end-effector Cartesian commands, sent to the robot via [Polymetis](https://github.com/facebookresearch/fairo/tree/main/polymetis) for realtime control. A browser page (`quest_controller.html`) hosted from the robot machine streams controller pose and button state from the headset itself, so no extra tracking hardware is needed.
- **Recording**: each demonstration ("episode") logs end-effector pose and gripper state at 10 Hz, alongside synchronized RGB frames from both cameras at 30 Hz, direct to an SSD mounted on the robot machine.
- **Dataset conversion**: recorded episodes are converted into the Zarr format the [Diffusion Policy](/projects/diffusion-policy/) training pipeline consumes, which also covers how the resulting policy gets deployed back onto this same arm.

<div class="d-flex flex-column align-items-center mt-3">
    <video autoplay loop muted playsinline style="max-width: 100%; border-radius: 8px;">
        <source src="{{ 'assets/video/panda_express_vr_teleop.mp4' | relative_url }}" type="video/mp4">
    </video>
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Collecting a demonstration via Meta Quest 3 Pro teleoperation.
    </div>
</div>

## Hardware setup

- **Arm**: Franka Emika Panda, controlled via Polymetis for low-latency Cartesian control.
- **Cameras**: two Intel RealSense D435s — one external, one wrist-mounted — for the dual-view observations the policy is trained on.
- **Compute split**: a dedicated robot-control machine runs the teleop/recording/client code, while training and inference run on a separate GPU machine, kept in sync over the network.

## Notable engineering details

A few things that weren't obvious going in:

- **The headset has to be worn, not just powered on** — the controller's pose is tracked relative to the headset, so without it on your head the poses come out meaningless. Cost real debugging time before the fix turned out to be that simple.
- **Camera calibration matters more than expected** — since it's easy to bump a camera between recording sessions, an uncalibrated dual-camera setup makes the trained policy noticeably more brittle than a single fixed view.
- **Gripper control needed a real fix upstream** — the stock Polymetis gripper client didn't expose a blocking stop command, so I forked and patched it (tracked against [facebookresearch/fairo#1417](https://github.com/facebookresearch/fairo/pull/1417)) rather than working around it in the teleop script.
- **Deliberately diverse demonstrations** — varying lighting, object placement, and even recording recovery actions after a failed grasp, rather than only clean successful episodes, since that diversity is what the eventual policy generalizes from.

## Code

[github.com/sanger640/panda_express](https://github.com/sanger640/panda_express)
