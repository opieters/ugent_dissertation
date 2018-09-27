---
title: Conclusion
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 8
---

In this master dissertation, the design of a WMS system is considered for the measurement of CO and CO~2~ gas concentrations in ambient conditions. The system is designed to be performant and cost-effective, and a prototype has been constructed to validate performance.

First, different system architectures were explored. The final architecture is a trade-off between design complexity and efficiency and should scale without much redesign to different gasses and lasers than those considered in this dissertation.

From this architecture, we designed both a laser driver and detector. Signal generation is an important aspect of the system. First a comparison is made between analogue generation using dedicated circuits, digital generation using DAC with, and without filters. The digital generation without explicit filtering was selected as generation technique for its simplicity and good phase-locking properties. Then a custom 6bit DAC is designed for the both modulation signals. These signals are then combined using an analogue adder circuit that also adds the bias signal to set the laser into its correct operating point. Finally, these voltage signals are converted using a linear voltage controlled current source to drive the laser. This source can easily be adopted for different lasers.

The signal then passes through the gas cell, which can be a free space environment for ambient concentration measurement, but also an on-chip spiral structure that can be integrated into other products. A power budget calculation is made for three possible set-ups. From this power budget calculation, the detector chain is designed. This starts with the TIA design, which needs to provide sufficient amplification such that noise of subsequent stages can be neglected. Stability is also an important issue with TIA design. Next, the low- and high-pass filters are designed using the Sallen-Key topology. These filter out undesired components for gas concentration detection such as the DC bias and bias modulation. A VGA is also designed to provide additional gain in case the optical set-up is changed, alleviating the need to update the electronics also. Finally, a 16bit differential ADC is selected and a single-end to differential converted is designed.

Correct functioning of the system not only requires performant hardware, but also good software. The microcontroller forms the beating heart of the system by not only controlling the input of the system by means of its digital IO-pins, but also sending the sample signal and reading the digital data from the ADC over SPI. The microcontroller also performs the two LIA operations for the 1f and 2f components. The data is then sent to a computer for further processing. The computer receives the data and can then extract the concentration by simulating the entire electro-optical system and comparing the simulated $R\_{2/1}$ ratio to the measured ratio.

To validate performance of this architecture and each of the components, a prototype PCB has been built. This prototype features all of the designed electrical components and links these to the optical ones. Measurements on each of the electronic circuits have been performed (if possible) to verify if the behaviour corresponds well to the simulations. 
After performing these preliminary electronic measurements, the system is tested by measuring the CO~2~ concentration at ambient levels and increased levels. The initial set-up has too many harmonics, but these are mostly removed after a small redesign of the bias modulation DAC. Furthermore, a gain offset between the two main lobes of $R\_{2/1}$ is observed. The TEC might be the case of this problem. It attempts to correct for the slight heat-up (and cool down) due the bias modulation signal. As a consequence, the frequency of the bias modulation is lowered after a redesign to alleviate these problems. For increased performance, future designs should have an optimised TEC though.

Finally, the system still has ample room for improvements. The most pressing issue is the TEC. A custom circuit needs to be designed such that the system can operate as designed. Relating to the TEC is calibration to the absorption peak centre. Currently, this is performed manually, but in the future - when the microcontroller has control over the TEC, a dedicated calibration algorithm is needed. Furthermore, a dedicated background subtraction might be needed to achieve sufficient accuracy.
