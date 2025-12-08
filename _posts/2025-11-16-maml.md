---
layout: distill
title: Model-Agnostic Meta-Learning for Fast Adaption of Deep Networks
date: 2025-11-16
description: 
tags: representation learning
categories: explanation
related_posts: false

bibliography: paper.bib
---
# Introduction
- Meta-learning: learning to learn
  - Goal of the trained model is to quickly learn a new task from a small amount of new data
  - Model is trained by the meta-learner to be able to learn on a large number of different tasks
- Key Idea: train model's initial params such that model has maximal performance on a new task after the params have been updated through few gradients steps with some small data for the new task.
  - Can be viewed as building an internal representation which is suitable to many tasks, fine-tuning the params slightly for good perform.
  - the process optimizes for model that are fast and easy to fintune
- From dynamical systems view, maxing the sensitivity of the loss functions of new tasks with respect to the params
  - when sensitivity is high, small local changes to params can lead to largements in the task loss.
- Model and task-agnostic algorithm

# Model-Agnostic Meta-Learning
## Meta-Learning Problem Setup
- meta-learning problem treats entire tasks as "training examples"
- Training phase:
  - Sample a task from a distribution of task
  - Sample K input samples from the task
  - Get output from model
  - calculate loss of task
  - test it on unseen data from the task
  - model improved by considering the test error on unseen data changes with parameters (this is training error of the meta-learning process)
- Generally, tasks used for meta-testing are held out during meta-training.
  
## MAML Algorithm
- Intuition: some internal representations are more transferrable than others
  - NN might learn internal features that are broadly applicable to all tasks, rather than a single task
  - How to encourage the emergence of such general purpose representations?
- **Aim to find model parameters that are sensitive to changes in the task**
  - Small changes in parameters will produce large improvements in loss function of any task when altered in the direction of the gradient of that loss.
- Only assumption: loss function is smooth enought that gradient based techniques can be applied.
- Meta-optimization is performed over the model params, wheras the objective is computed using updated model params.
  - MAML meta-gradient updates involves a gradient through a gradient
  - Meta Objective : $$$$