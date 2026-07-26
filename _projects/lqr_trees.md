---
layout: page
title: LQR-Trees for Feedback Motion Planning
description: Lyapunov funnels and sums-of-squares programming to build regions of attraction and stabilize trajectories on a sparse randomized tree
permalink: /projects/lqr-trees/
img: assets/img/funnelSeq.png
importance: 5
category: research
github: https://github.com/sanger640/lqr_trees
---

## What it is

An implementation of [LQR-Trees](https://groups.csail.mit.edu/robotics-center/public_papers/Tedrake09.pdf) (Tedrake et al.) — a feedback motion-planning method that builds a sparse tree of trajectories, each one wrapped in a "funnel" of states guaranteed to converge to it. Instead of planning a single trajectory and hoping a low-level controller keeps you on it, LQR-Trees explicitly certify a *region* around each trajectory from which the system provably converges, so nearby trajectories in the tree can be linked into a full feedback policy over a much larger part of the state space. I implemented it around a nonlinear pendulum.

## How it works

- **Time-invariant funnel at the goal**: solve the algebraic Riccati equation for the LQR problem linearized at the goal to get a quadratic cost-to-go, use that as a Lyapunov function candidate, and solve a sums-of-squares (SOS) program — with input saturation constraints — to find the largest sublevel set (the "funnel") that's a certified region of attraction.
- **Trajectory stabilization**: generate an open-loop trajectory from an initial point to the goal via direct collocation, then use time-varying LQR (backward Riccati recursion along the trajectory) to get a stabilizing feedback gain $K(t)$ and cost-to-go $S(t)$ at every point, fit with a cubic spline.
- **Tree growth (proof of concept)**: sample a random point, find its nearest neighbour on the existing tree, and connect it with a new direct-collocation trajectory — the RRT-style piece of the algorithm.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/funnelSeq.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Sequential funnels along a trajectory — each one a certified region of attraction that hands off to the next.
    </div>
</div>

## Where it got hard

Most of the real difficulty was in the SOS formulation, not the planning logic:

- Getting comfortable with symbolic optimization for SOS programming was the first hurdle — I was new to the tooling (Casadi and Drake) and it took a while to understand how to correctly pose the sums-of-squares problem.
- With input saturation included, the time-invariant case needs 7 Lagrange multipliers in the SOS formulation, and trying to solve for both $\rho$ (the funnel size) and the multiplier coefficients simultaneously kept introducing nonlinear cross-terms. Switching to a **linear search over $\rho$** instead of solving for it jointly sidestepped the issue, and for the time-invariant case at the goal, the resulting region-of-attraction estimate matched the original paper's numbers closely under the same parameters.
- The time-varying case — generating $\rho(t)$ along a trajectory while optimizing over a spline with the same saturation constraint — I wasn't able to formulate correctly in the time available, so I can't yet reproduce the full funnel sequence end-to-end. That's the natural next step.

## Code

[github.com/sanger640/lqr_trees](https://github.com/sanger640/lqr_trees)
