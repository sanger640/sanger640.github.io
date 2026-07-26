---
layout: page
title: Diffusion Policy for Real-Robot Manipulation
description: Adapting Diffusion Policy to dual-camera Franka Panda manipulation, with a real-time serving architecture for closed-loop control
permalink: /projects/diffusion-policy/
img: assets/img/diffusion_policy_arch.svg
importance: 2
category: research
github: https://github.com/sanger640/diffusion_policy
---

## What it is

[Diffusion Policy](https://diffusion-policy.cs.columbia.edu/) (Chi et al., RSS 2023) frames visuomotor robot control as a conditional denoising process: instead of regressing directly to a single action, the policy learns to iteratively denoise a randomly-initialized action sequence conditioned on the current observation. That lets it represent the multimodal, discontinuous action distributions that show up constantly in contact-rich manipulation (e.g. "grasp from the left or the right, both are fine") far better than a standard behavior-cloning regression head.

I forked the [original Columbia/real-stanford codebase](https://github.com/real-stanford/diffusion_policy) — built and benchmarked around simulated tasks like PushT — to train and deploy it on our lab's Franka Panda arm for a real, contact-rich pick-and-place task.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/diffusion_policy_arch.svg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Serving architecture: dual-camera observations flow into the diffusion policy, which is queried by the robot's control loop over a persistent connection for closed-loop execution.
    </div>
</div>

## How it works

- Human teleoperation demonstrations (collected via the VR teleop pipeline in [VR Teleop and Diffusion Policy](/projects/)) are converted into the Zarr dataset format the training pipeline expects.
- A U-Net-based diffusion model is trained to denoise short action-sequence "chunks," conditioned on a short history of visual + proprioceptive observations.
- At inference, the model runs the reverse diffusion process to sample an action chunk, executes part of it on the robot, then re-observes and re-plans — the same receding-horizon idea as MPC, but with a diffusion model instead of an optimizer in the loop.

## My contributions

Starting from a codebase built for single-camera simulated benchmarks, I adapted it for a real dual-camera manipulation task end-to-end:

- **New real-world task**: added a "franka cup" task — dataset loader, image-based task config, and training config for a custom cup manipulation demonstration set collected on the physical arm (`cup_dataset.py`, `train_franka_cup.yaml`).
- **Dual-camera perception**: extended the single-view pipeline to a front + wrist camera setup, including a new Zarr conversion script and dataset class for the two-camera data (`create_dual_zarr_proc.py`, `cup_dual_dataset.py`/`cup_dual_dataset_proc.py`).
- **Real-robot inference**: since the original repo only evaluates in simulated gym environments, I wrote a `inference.py` script plus a "dummy" env runner (`dummy_runner.py`, `eval_dummy.py`) so the trained policy could be rolled out and evaluated directly on hardware instead of in a simulator.
- **Model serving**: built `diff_server.py` / `diff_server_dual.py`, a lightweight client-server layer that hosts the trained policy on a GPU machine and serves action predictions to the robot's control loop in real time, decoupling training/inference hardware from the robot's control computer.

## Code

The full fork, including the dual-camera task configs, real-robot inference scripts, and serving code described above, is available on GitHub:

[github.com/sanger640/diffusion_policy](https://github.com/sanger640/diffusion_policy)
