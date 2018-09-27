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

In this master dissertation, the design of a <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> system is considered for the measurement of <abbr title="Carbon Monoxide">CO</abbr> and <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> gas concentrations in ambient conditions. The system is designed to be performant and cost-effective, and a prototype has been constructed to validate performance.

First, different system architectures were explored. The final architecture is a trade-off between design complexity and efficiency and should scale without much redesign to different gasses and lasers than those considered in this dissertation.

From this architecture, we designed both a laser driver and detector. Signal generation is an important aspect of the system. First a comparison is made between analogue generation using dedicated circuits, digital generation using <abbr title="Digital-to-Analogue Converter">DAC</abbr> with, and without filters. The digital generation without explicit filtering was selected as generation technique for its simplicity and good phase-locking properties. Then a custom 6bit <abbr title="Digital-to-Analogue Converter">DAC</abbr> is designed for the both modulation signals. These signals are then combined using an analogue adder circuit that also adds the bias signal to set the laser into its correct operating point. Finally, these voltage signals are converted using a linear voltage controlled current source to drive the laser. This source can easily be adopted for different lasers.

The signal then passes through the gas cell, which can be a free space environment for ambient concentration measurement, but also an on-chip spiral structure that can be integrated into other products. A power budget calculation is made for three possible set-ups. From this power budget calculation, the detector chain is designed. This starts with the <abbr title="Transimpedance Amplifier">TIA</abbr> design, which needs to provide sufficient amplification such that noise of subsequent stages can be neglected. Stability is also an important issue with <abbr title="Transimpedance Amplifier">TIA</abbr> design. Next, the low- and high-pass filters are designed using the Sallen-Key topology. These filter out undesired components for gas concentration detection such as the <abbr title="Direct Current">DC</abbr> bias and bias modulation. A <abbr title="Variable Gain Amplifier">VGA</abbr> is also designed to provide additional gain in case the optical set-up is changed, alleviating the need to update the electronics also. Finally, a 16bit differential <abbr title="Analogue-to-Digital Converter">ADC</abbr> is selected and a single-end to differential converted is designed.

Correct functioning of the system not only requires performant hardware, but also good software. The microcontroller forms the beating heart of the system by not only controlling the input of the system by means of its digital <abbr title="Input/Output">IO</abbr>-pins, but also sending the sample signal and reading the digital data from the <abbr title="Analogue-to-Digital Converter">ADC</abbr> over <abbr title="Serial Peripheral Interface">SPI</abbr>. The microcontroller also performs the two <abbr title="Lock-In Amplifier">LIA</abbr> operations for the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. The data is then sent to a computer for further processing. The computer receives the data and can then extract the concentration by simulating the entire electro-optical system and comparing the simulated $R\_{2/1}$ ratio to the measured ratio.

To validate performance of this architecture and each of the components, a prototype <abbr title="Printed Circuit Board">PCB</abbr> has been built. This prototype features all of the designed electrical components and links these to the optical ones. Measurements on each of the electronic circuits have been performed (if possible) to verify if the behaviour corresponds well to the simulations. 
After performing these preliminary electronic measurements, the system is tested by measuring the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration at ambient levels and increased levels. The initial set-up has too many harmonics, but these are mostly removed after a small redesign of the bias modulation <abbr title="Digital-to-Analogue Converter">DAC</abbr>. Furthermore, a gain offset between the two main lobes of $R\_{2/1}$ is observed. The <abbr title="Thermoelectric Cooling">TEC</abbr> might be the case of this problem. It attempts to correct for the slight heat-up (and cool down) due the bias modulation signal. As a consequence, the frequency of the bias modulation is lowered after a redesign to alleviate these problems. For increased performance, future designs should have an optimised <abbr title="Thermoelectric Cooling">TEC</abbr> though.

Finally, the system still has ample room for improvements. The most pressing issue is the <abbr title="Thermoelectric Cooling">TEC</abbr>. A custom circuit needs to be designed such that the system can operate as designed. Relating to the <abbr title="Thermoelectric Cooling">TEC</abbr> is calibration to the absorption peak centre. Currently, this is performed manually, but in the future - when the microcontroller has control over the <abbr title="Thermoelectric Cooling">TEC</abbr>, a dedicated calibration algorithm is needed. Furthermore, a dedicated background subtraction might be needed to achieve sufficient accuracy.

<ul>
</ul>
