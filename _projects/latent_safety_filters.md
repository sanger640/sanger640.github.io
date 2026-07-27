---
layout: page
title: "Latent Safety Filters: A Stability-Centric Safety Monitor for Manipulation"
description: A label-free safety monitor that flags unsafe actions from a learned manipulation policy before they execute, by checking the stability of a learned world model's rollouts
permalink: /projects/latent-safety-filters/
img: assets/img/safety_monitor_architecture.png
importance: 2
category: research
github: https://github.com/sanger640/dino_wm
---

This is my main research direction under Prof. Soo Jeon's group at Waterloo, spanning three repos: [VR Teleop Data Collection](/projects/panda-express/), [Diffusion Policy](/projects/diffusion-policy/) for the manipulation policy itself, and [DINO-WM](https://github.com/sanger640/dino_wm) for the world model the safety monitor is built on.

## What it is

Diffusion policies and other learned visuomotor controllers are black boxes: they have no internal notion of "safety" and no way to specify a safety constraint at inference time. Classical collision-avoidance doesn't help either — manipulation is inherently contact-rich, and picking an item out of a cluttered dishwasher *requires* contact rather than avoiding it. Failure classifiers are the usual fallback, but they need labelled failure data, which is labour-intensive to collect and brittle to failure modes you didn't anticipate.

The Safety Monitor sits between the policy and the robot and asks a different question: not "does this look like a failure I've seen before," but "is this action about to push the system across an instability boundary?" — answered with no labelled failures at all.

## Core idea: instability as a failure signal

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/instability_toy_example.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        A 1D toy example: perturbing a block's angle near a stability boundary (case 3) leads to an unpredictable final state, while the same perturbation elsewhere (cases 1, 2, 4, 5) always settles back to the same outcome.
    </div>
</div>

Take a block balanced somewhere between standing and lying flat. Perturb its angle slightly: far from the tipping point, it always settles back to the same state (still standing, or still fallen). Near the tipping point, the same small perturbation produces wildly different outcomes — that divergence *is* the signature of an unsafe regime.

The hypothesis behind this project is that this generalizes directly to manipulation: replace the block's angle with the full robot+scene state, and replace the physical perturbation with small changes to the policy's proposed action sequence. Near a real failure boundary — a block about to topple, a grasp about to slip, a liquid about to spill — small action perturbations should produce large, divergent outcomes in a learned model of the dynamics. Far from any failure boundary, they shouldn't.

## How it works

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/safety_monitor_architecture.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Safety Monitor architecture: a Deviator Agent perturbs the policy's proposed action, a world model rolls out both the nominal and perturbed actions in latent space, and a Divergence Detector flags the action as safe or a failure.
    </div>
</div>

1. **World model**: a DINO-WM-based model, trained on rollout data, that predicts future latent states from a history of observations and actions — letting us "imagine" the outcome of an action sequence before running it on the real robot.
2. **Deviator Agent**: given the policy's proposed action sequence, generates a set of N nearby, noisy action sequences by Gaussian-perturbing it:

    $$a_{0:T} \;\rightarrow\; \tilde{a}_{1,0:T}, \; \tilde{a}_{2,0:T}, \; \dots, \; \tilde{a}_{N,0:T}$$

3. **Divergence Detector**: rolls the world model forward under the nominal action *and* every perturbed action, then measures how far the perturbed rollouts diverge from the nominal one. Large divergence → **failure**; small, consistent divergence → **safe**.

### Task setup

<div class="d-flex flex-column align-items-center mt-3">
    <video autoplay loop muted playsinline style="max-width: 100%; border-radius: 8px;">
        <source src="{{ 'assets/video/safety_monitor_task_demo.mp4' | relative_url }}" type="video/mp4">
    </video>
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        MuJoCo task: pick up the red block without toppling the adjacent blocks.
    </div>
</div>

The policy's goal is to pick up a red block from a small stack without toppling the blocks next to it — a deliberately contact-rich, Jenga-like task. The diffusion policy sees two camera views (wrist + top-down) and outputs an end-effector pose plus a gripper open/close command; it was trained on 100 teleoperated demonstrations and evaluated over 70 rollouts, reaching a 72% task success rate on its own.

## Results

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/safety_monitor_eval.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Ground truth vs. the world model's nominal prediction vs. its worst-case perturbed rollout — the red box marks a topple the monitor correctly caught.
    </div>
</div>

Evaluated against MuJoCo ground truth over 70 pick-and-place rollouts:

| Accuracy | Precision | Recall | F1 |
|:---:|:---:|:---:|:---:|
| 96.9% | 65.9% | 59.2% | 62.4% |

- **Accuracy (96.9%)**: the monitor rarely raises spurious flags, so it doesn't get in the way of a policy that's already succeeding.
- **Precision (~66%)**: when it does flag an action as unsafe, it's right about two-thirds of the time.
- **Recall (~59%)**: it catches a bit under two-thirds of the truly unsafe actions the policy produces.

## Limitations & future directions

- **Identical cues**: some stable and unstable configurations look visually indistinguishable from the current camera angle — a different viewpoint is needed to see the cue that predicts a fall.
- **Prediction artifacts**: because the world model predicts the whole latent scene, hard-to-model background details (shadows, patterns, reflections) can trigger false positives that have nothing to do with the task.
- **ROI filtering via PCA**: to address that, I'm exploring applying PCA to the world model's latent features to mask out background dimensions, so divergence is measured mostly over the parts of the latent space that actually correspond to the manipulated objects.
- **Multi-modal sensing**: a force/torque sensor at the end-effector would give a direct instability signal even when the camera is occluded, and additional camera views would make instability cues more observable in general.

## Potential applications

- **Object handover** — safely passing items to a person (a glass of water, medicine, a food tray) without dropping or crushing them.
- **Hazardous material handling** — catching instability before it causes a spill or breakage of lab vials, flammables, or fragile samples.
- **Picking in clutter** — correcting unstable grasps when retrieving objects from dishwashers, cabinets, or fridges.
- **Medical device assembly** — precision insertion of catheters, implants, or surgical tools, where instability can damage delicate components.

## My contributions across the stack

This project ties together all three of my robot-learning repos:

<div class="d-flex flex-column align-items-center mt-3">
    <video autoplay loop muted playsinline style="max-width: 100%; border-radius: 8px;">
        <source src="{{ 'assets/video/teleop_demo.mp4' | relative_url }}" type="video/mp4">
    </video>
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Collecting demonstrations via spacemouse teleop — the data both the policy and the world model are trained on.
    </div>
</div>

- **[VR Teleop Data Collection](/projects/panda-express/) (`panda_express`)**: the teleop app used to collect the demonstration dataset above, and the integration layer that runs the trained policy on the physical Franka Panda.
- **[Diffusion Policy](/projects/diffusion-policy/) (`diffusion_policy` fork)**: the visuomotor policy being monitored, plus the real-time serving layer that puts it in the control loop.
- **`dino_wm` fork**: the latent world model that powers the Deviator Agent and Divergence Detector, extended to a dual-camera setup with per-camera prediction heads, and the PCA/k-means-based latent filtering I'm using to explore the ROI-filtering fix above.

## Code

- [github.com/sanger640/panda_express](https://github.com/sanger640/panda_express) — VR teleop data collection and robot integration
- [github.com/sanger640/diffusion_policy](https://github.com/sanger640/diffusion_policy) — policy training and serving
- [github.com/sanger640/dino_wm](https://github.com/sanger640/dino_wm) — world model, safety monitor, and PCA-based latent filtering
