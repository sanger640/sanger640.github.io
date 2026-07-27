---
layout: page
title: Cartesian Impedance Control for Franka Panda
description: Null-space projected Cartesian impedance controller for a 7-DOF Franka Panda, simulated in MuJoCo with Pinocchio for kinematics/dynamics
permalink: /projects/impedance-controller/
img: assets/img/impedance_controller_thumb.png
importance: 4
category: research
github: https://github.com/sanger640/impedance_controller
---

## What it is

A Cartesian impedance controller for a 7-DOF Franka Emika Panda, simulated in MuJoCo with [Pinocchio](https://github.com/stack-of-tasks/pinocchio) handling the kinematics and rigid-body dynamics. Rather than commanding joint positions directly, the controller makes the end-effector behave like a virtual spring-damper toward a target pose — letting the arm comply with unexpected contact instead of fighting it, which matters for any task where contact is expected (as opposed to purely free-space motion).

## How it works

- At each control step, Pinocchio computes the end-effector Jacobian and the current Cartesian pose/velocity from the robot's joint state.
- Desired joint torques are computed from the Cartesian pose and velocity error against a target pose, through a stiffness/damping law mapped back into joint space via the Jacobian transpose.
- Because the Panda has 7 joints for a 6-DOF Cartesian task, there's one redundant degree of freedom — a **null-space projection** term adds secondary damping in that redundant direction, so the arm doesn't drift or oscillate in ways that don't affect the end-effector pose.
- Torques are applied directly as MuJoCo actuator commands, closing the loop in simulation.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/impedance_controller.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Tracking results across three scenarios (free-space, wall contact, disturbance) and three stiffness/damping configurations, plus the estimated external force recovered from tracking error.
    </div>
</div>

## Scenarios

The repo evaluates three configurations — Stiff & Critically Damped, Low-Stiffness & Critically Damped, and Low-Stiffness & Underdamped — across three tasks:

- **Free-space**: tracking a Cartesian trajectory with nothing in the way.
- **Wall-contact**: tracking the same trajectory into a rigid obstacle, where a stiffer controller pushes harder against the wall while a compliant one yields.
- **Disturbance**: an external push mid-trajectory, testing how each configuration recovers.

The estimated external force plots (rightmost column above) come directly from the tracking-error term in the impedance law, without any force/torque sensor — a compliant controller effectively gives you a rough contact-force estimate for free.

## Code

[github.com/sanger640/impedance_controller](https://github.com/sanger640/impedance_controller)
