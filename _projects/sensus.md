---
layout: page
title: "Sensus: Electro-Tactile Braille Interface"
description: A wearable interface that renders braille on the fingertip through electro-tactile stimulation, giving people with dual sensory loss passive access to text
permalink: /projects/sensus/
img: assets/img/sensus_poster.jpg
importance: 7
category: design
---

## What it is

My undergraduate capstone (Team #02, advised by Dr. Yue Hu): a wearable interface that lets people with **dual sensory loss** — combined vision and hearing impairment, affecting roughly 466,000 Canadians — read text from a smart device through touch alone.

Screen readers assume hearing. Refreshable braille displays solve that, but they're solenoid-actuated, bulky to carry, cost on the order of $2,600–3,000, and demand continual conscious attention from the reader. Sensus renders braille with *electrical* stimulation instead of moving pins, which is what makes it small enough to wear.

The framing came from Debbie Gillespie, former National Braille Coordinator at CNIB, who told us it "could make reading braille relaxing rather than taxing… passive rather than conscious." The target was braille that feels as effortless as listening to an audiobook.

<div class="d-flex flex-column align-items-center mt-3">
    {% include figure.liquid loading="eager" path="assets/img/sensus_poster.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    <div class="caption mt-2" style="max-width: 100%; text-align: center;">
        Capstone design symposium poster — problem framing, hardware chain, stimulation approach, and user testing results.
    </div>
</div>

## How it works

A command from a paired device becomes a braille cell felt at the fingertip:

- A **microcontroller** takes the digital input and drives a **switch array**, which opens and closes outputs to individual electrodes — that's what selects which dots of the braille cell are "raised."
- An **amplification PCB** steps a small AC signal up using a DC supply from the battery pack, and a **flex PCB** carries those outputs to an **electrode array** wrapped around the fingertip, passing current across the skin to stimulate the mechanoreceptors below.
- Stimulation uses **amplitude modulation** — a signal wave riding a carrier — which dramatically reduces the voltage needed to produce a reliable sensation. Raising the signal frequency makes the sensation smoother, and the modulation depth doubles as a comfort control, since the intensity people tolerate varies widely.

Nothing actuates mechanically, so the device stays compact and the hand stays free.

## My role

I owned **software and controls**: the stimulation signal generation and modulation scheme, and the control logic mapping incoming text to electrode switching patterns. Aidan Creaser managed the project, Jaskaran Narwal and Daniel Featherby led electrical and circuit design, and Gauthamkrishna Anil led mechanical.

## Results

We validated the prototype with **10 participants** across three escalating tasks: locating 10 individual dots on the array, reading 5 displayed numbers, and reading 3 displayed letters. Accuracy was strongest on dot localization and fell off as tasks moved from single dots to full characters — enough to show electro-tactile braille is viable in a wearable form factor, while pointing squarely at character-level resolution as the thing to improve next. Testing also identified a preferred stimulation frequency band across participants.
