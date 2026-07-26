---
layout: page
title: "DINO-WM: Latent World Models for Contact-Rich Manipulation"
description: Scaling a DINOv2-based world model to dual-camera Jenga manipulation, with a latent safety-filter head and multi-GPU cluster training
permalink: /projects/dino-wm/
img: assets/img/dino_wm_arch.svg
importance: 3
category: research
github: https://github.com/sanger640/dino_wm
---

## What it is

[DINO-WM](https://dino-wm.github.io/) (Zhou et al., NYU/Meta) is a world model that predicts *future DINOv2 visual embeddings* — rather than future pixels — from a history of observations and actions. Because it plans entirely in a pretrained visual feature space, it can do zero-shot model-predictive control (via CEM/MPPI-style sampling) on new tasks without any task-specific reward learning or pixel-level reconstruction.

I forked the [original codebase](https://github.com/gaoyuezhou/dino_wm) to apply it to a Jenga block-extraction task — a genuinely contact-rich manipulation problem, since success depends on sensing and reasoning about contact forces as a block is slid out of the tower, not just end-effector position.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/dino_wm_arch.svg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Dual cameras and proprioception feed a shared DINOv2 encoder; a predictor rolls the state forward in latent space for planning, while training runs at scale on a multi-GPU SLURM cluster.
    </div>
</div>

## How it works

- Front and wrist camera observations (plus proprioception) are encoded independently through a frozen DINOv2 backbone.
- A predictor network rolls the encoded state forward in latent space conditioned on candidate action sequences.
- A sampling-based planner (CEM/MPPI) searches over action sequences, scoring each by how close its predicted latent rollout gets to a goal embedding, then executes the best sequence in a receding-horizon loop.

## My contributions

The public repo targets single-camera, small-scale simulated tasks. Getting it to a dual-camera Jenga task at a scale that needed real cluster compute meant extending the model, the data pipeline, and the infrastructure:

- **Dual-camera world model**: extended the single-view `VWorldModel` into a dual-view architecture (`models/dual_visual_world_model.py`) with independent front/wrist decoder heads, plus latent MLP heads for each camera stream — connecting this world model to the latent safety-filter idea from my own [safety filters research](/blog/2025/safe/) for flagging unsafe contact states directly in latent space.
- **Jenga task + data pipeline**: built the environment config and several generations of the dataset pipeline (`conf/env/jenga.yaml`, `datasets/jenga_dset*.py`) as I iterated on how to store and stream a large image/action/proprioception dataset efficiently — moving from plain pickle files to a full LMDB-backed pipeline (`create_lmdb_full.py`, `create_lmdb_par.py`) to keep GPU utilization high during training.
- **Multi-GPU cluster training**: got 4×H100 distributed training (`train_dual.py`, Hydra + `submitit`) running reliably on the Digital Research Alliance of Canada's Nibi/Narval/Rorqual clusters — SLURM allocation scripts, NCCL/offline-mode configuration for compute nodes with no internet access, and staging datasets onto fast local `$SLURM_TMPDIR` storage for each job.
- **Low-latency serving**: built several model-serving variants (`server_single.py`, `server_pca.py`, `server_cluster.py`) over ZeroMQ, including a PCA/k-means-compressed latent variant, to keep the world model's real-time planning queries fast enough for closed-loop control on the physical arm.

## Code

The full fork, including the dual-camera world model, LMDB data pipeline, cluster training scripts, and serving variants described above, is available on GitHub:

[github.com/sanger640/dino_wm](https://github.com/sanger640/dino_wm)
