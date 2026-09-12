---
layout: page
title: "Sensus: Electro-Tactile Braille Interface"
description: A glove-worn haptic interface that renders braille on the fingertip through electro-tactile stimulation, for people with dual sensory loss
permalink: /projects/sensus/
img: assets/img/sensus_diagram.svg
importance: 7
category: design
---

## What it is

My undergraduate capstone: a wearable interface that lets people with **dual sensory loss** (combined vision and hearing impairment) read text from a smart device through touch alone. Screen readers assume hearing; refreshable braille displays are bulky, expensive, and occupy the hand. Sensus renders braille directly onto the fingertip using electrical stimulation instead of moving pins, which is what makes it small enough to wear as a glove.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/sensus_diagram.svg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Text from a paired device is encoded into braille cells and delivered as localized electro-tactile sensations at the fingertip.
    </div>
</div>

## How it works

- Text from a paired smart device — a book, a message, a notification — is encoded into braille cells.
- Each cell is delivered as **electro-tactile stimulation** through an electrode array at the fingertip, so a dot is a localized sensation rather than a raised pin.
- Using **AC stimulation** gives finer localization and lets the sensation be moved across the fingertip, while keeping the electronics small enough to stay portable.
- An **amplitude-based control scheme** lets the wearer tune how rough or sharp the stimulation feels, since comfortable intensity varies a lot between people.

Because nothing mechanically actuates, the device stays compact and the hand stays free — the wearer can take in text passively while doing something else.

## My role

I led a five-person multidisciplinary team spanning electrical, mechanical, and controls work, taking the concept from problem definition to a working prototype. We consulted Debbie Gillespie at CNIB on dual sensory loss and braille literacy to ground the design in how people actually read braille, and were advised by Yue Hu and Kamyar Ghavam.

## Outcome

The team delivered a functioning prototype demonstrating that electro-tactile braille rendering is viable in a wearable form factor. [Project summary on LinkedIn](https://www.linkedin.com/feed/update/urn:li:activity:7182604649739235328/).
