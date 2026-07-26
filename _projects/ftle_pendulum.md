---
layout: page
title: Chaos-Aware Sensitivity Analysis of RL Policies
description: Using Finite-Time Lyapunov Exponents to analyze the sensitivity of SAC/PPO policies trained on a sparse-reward pendulum swing-up task
permalink: /projects/ftle-pendulum/
img: assets/img/ftle_pendulum_method.svg
importance: 6
category: research
github: https://github.com/sanger640/ftle_pendulum
---

## What it is

A study of how sensitive a trained RL policy's closed-loop behavior is to small changes in initial conditions, using a **sparse-reward** pendulum swing-up task — the policy only gets a reward if it ends the episode upright and nearly still, with no shaping reward along the way, which makes the control problem noticeably harder than the standard dense-reward version. I trained both SAC and PPO agents (via Stable-Baselines3) on this task and then asked: near which states does the *trained policy's* closed-loop behavior become unpredictable under tiny perturbations?

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/ftle_pendulum_method.svg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Method: roll out the trained policy from a nominal state and from a set of Gaussian-perturbed nearby states, then compare how far the resulting trajectories diverge in phase space.
    </div>
</div>

## How it works

- Roll out the trained policy from a nominal initial state (a starting angle and angular velocity), recording the full trajectory.
- Perturb that initial state with Gaussian noise, independently on angle and angular velocity, and roll out the same policy from each perturbed start.
- Compare the perturbed trajectories against the nominal one in phase space (angle vs. angular velocity): if they track closely, the policy is locally stable there; if they fan out into very different outcomes, that state is close to a boundary between success and failure.
- Repeat across many initial states to build up a picture of where the trained policy's basin of attraction is fragile versus robust, for both SAC and PPO.

## Why it matters

This was an early, low-cost testbed for an idea that turned out to generalize much further: **treating trajectory divergence under small perturbations as a label-free signal for approaching failure**, without needing any hand-labelled failure data. That same perturb-and-compare mechanism is the core of the [Latent Safety Filters](/projects/latent-safety-filters/) safety monitor I later built for real robot manipulation — the pendulum version made it cheap to iterate on the idea before scaling it up to a learned visual world model and a real Franka Panda.

## Code

[github.com/sanger640/ftle_pendulum](https://github.com/sanger640/ftle_pendulum)
