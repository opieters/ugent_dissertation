---
title: System Architecture and Specifications
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 2
---

In this chapter, we discuss the set of specifications that the system should meet. These consist of measurement related specifications and electrical specifications. In the second part of this chapter, the system architecture is highlighted and compared to alternatives. In the subsequent \namecrefs{system}, each of the components from this architecture will be implemented and discussed. 

# System Specifications

Before starting the design, a set of specifications is needed. These are mentioned in this section. We will start with the measurement related specifications, followed by the electrical ones. There are no real optical specifications, since the optical components (laser and photodiode) are fixed beforehand. 

The system should have sufficient accuracy such that it can measure ambient levels of CO~2~ and dangerous levels if CO. Ambient CO~2~ levels are around 410ppm [<a href="{{< relref "#citation1" >}}">1</a>] and the maximum allowed daily dose of CO is in the range of 35 to 200ppm continuous exposure per day [<a href="{{< relref "#citation2" >}}">2</a>]. As a consequence, we require that the system is able the measure concentrations with an uncertainty of up to \pm 10ppm.

These measurements should be performed in real-time, though a single measurement need not have an accuracy of \pm 10ppm, a combination of them should. Measurements can be combined by averaging them to decrease the noise. Since measurements are assumed to be performed in real-time, we require that \pm 10ppm accurate measurements are performed within 1s. 

As discussed in subsection <a href="{{< ref "background.md#subsection-1.1" >}}">1.1</a>, the system needs to capture two signals at different frequencies at the detector side. We require that the gain difference between both signals is at most 0.4dB, since this will directly result in concentration imprecision. During system design, we will work mostly with components that have a 5% tolerance, which justifies this 0.4dB offset gain offset (also approximately 5%). We also require that the entire system is able to run at a low supply voltage (e.g. 5V). This allows to scale the system more easily for integrated applications. 

# System Architecture

Prior to starting the system design, we must fix a system architecture. A general block diagram is provided in figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a>. We will now discuss why this architecture was selected and how it enables us to measure gas concentrations.

The architecture from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a> can be subdivided into three parts: the analogue/digital electronics (green blocks), optics (white blocks) and the microcontroller which implements several features in its programme code (blue blocks). This dissertation focusses on the electronics and digital part, a laser and photodetector were provided. The gas cell may vary depending on environment. 
The operation of this architecture is straightforward. We start from the microcontroller output (upper left) part of the diagram that controls the DAC. The  DAC generates a voltage signal consisting of a bias component, line scanning ramp (or triangle) and high frequency sine modulation. These are voltage signals, while lasers are usually current controlled. The voltage controlled current source ($V \rightarrow I  \text{conv.}$) converts this voltage signal into a current signal that drives the laser. 

The emitted laser line is sent to the gas cell and some of this light is incident on a PD. A current signal is generated depending on the amount of incident light on the active area of the PD. This (typically small) current is converted into a voltage and amplified by the TIA. The first and second order harmonics are the signals of interest (see chapter <a href="{{< ref "background.md#chapter-1.1" >}}">1.1</a>), not the slowly varying ramp (or triangle) signal nor the bias level. The varying ramp (or triangle) and bias signal are filtered out using a high pass filter. The resulting signal can possibly be amplified using a VGA that is controlled by the microcontroller. Before converting the analogue signal into a digital signal, low pass filtering is performed. This is done to remove spurious components due to interference and to prevent aliasing. 

The (external) ADC converts the analogue signal into a digital signal at a fixed sampling rate. This signal is then passed to the LIA. These extract the desired information (the second order harmonic $2f$) and reference signal (the first order harmonic $1f$) that are combined into a ratio ($R$). A model is then fitted to this ratio to extract the concentration.

The LIA will be implemented on the Explorer 16 Development Board with 100-pin PIM (ref. number: DM240001), a prototyping board from Microchip featuring the dsPIC33FJ256GP710A microcontroller. This microcontroller operates at 40MIPS (MIPS) and features optimised DSP routines and instructions. Additionally, it also includes a 12bit ADC. An external 16bit ADC was selected though, since these can provide more accurate analogue-to-digital conversion. As we will discuss in subsection <a href="{{< ref "detector.md#subsection-4.2" >}}">4.2</a>, this us allows to better detect signals near the 10ppm accuracy limit. Furthermore, the signal does also not need to be amplified to the DAC conversion extrema ($\pm V\_\text{ref}$) to achieve this precision. This is desirable, since it will not always be possible to achieve optimal gain due to the variable optical set-up. The actual concentration extraction is done on a computer, because of the limited computational power of the microcontroller.

![System architecture block diagram.](/images/architecture_block_diagram.svg){#fig:system:architecture_block_diagram}

The architecture from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a> provides a good trade-off between complexity and flexibility. The optical setup can easily be varied for different lasers and gasses. Emitted laser powers can vary drastically (e.g.  VCSEL lasers vs. DFB lasers, table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a>). The wide range of optical signal powers that are incident on the PD need not result in a low amplitude at the ADC. The VGA can further amplify the signal to suitable levels for ADC conversion. The use of filters (especially the high-pass filter after the TIA), allows to bias the laser for different absorption peaks without needing to change the detector, since the DC bias value and triangle signal are filtered out. The input signals can also be controlled easily (from the microcontroller), making for instance an investigation into the number of bits needed for accurate measurement possible. 

Lasers typically required cooling to obtain accurate output wavelengths, a TEC is needed for this. This component senses a thermistor that is integrated into the laser and cools the laser down or heats it up by means of the thermo-electric effect (also called Peltier effect). A variable current is thus passed through the system. In this first prototype design, we will not yet design a custom circuit for this, but use a commercial device from Thorlabs [<a href="{{< relref "#citation3" >}}">3</a>]. 

## Alternative Architectures

The architecture from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a> is not the only one. Many possible variations can be made. If the DC bias current and triangle wave amplitude coming from the PD is not too large compared to the sine wave amplitude, we can omit the low pass filter. Additionally, if the optical system is fixed, the VGA can also be replaced by a fixed gain amplifier or the TIA can provide more gain. 

However, we used the system from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a> because if offers flexibility and can easily be adopted for different optical settings with variable attenuation and different gasses of interest, since these will also have different properties.

Figure <a href="{{< ref "system.md#figure-2.1" >}}">2.1</a> depicts a very different topology: the first and second order harmonic voltage signals are separated by means of band pass filters before the analogue to digital conversion step. Using this type of topology, we can optimise gain for both first and second order harmonics. 

Several problems can arise with this architecture. Jitter, random differences in sample conversion timing, can be different for different analogue to digital converters (assuming these are external). Furthermore, the gain for the first and second order harmonics is no longer equal. There will be gain imbalance, even if the two VGA are set to the same gain due to component tolerances. This means that the fitting method will also have to take the gain difference into account (see chapter <a href="{{< ref "digital_control_processing.md#chapter-5.1" >}}">5.1</a>). The two harmonics of interest are also spaced quite close together (10kHz in the realised system, see chapter <a href="{{< ref "laser_driver.md#chapter-3.1" >}}">3.1</a>). This will make analogue (active) filtering difficult. Finally, the overall system cost and complexity are increased (more complicated filtering, fitting and the need for two ADC) compared to the architecture in figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a>.

![Alternative system architecture block diagram.](/images/alternative_architecture_block_diagram.svg){#fig:system:alternative_architecture_block_diagram}
