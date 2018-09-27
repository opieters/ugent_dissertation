---
title: Improvements and Future Possibilities
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 7
---

This dissertation is limited in time, and thus the prototypes that have been designed still have room for improvement. We will highlight these in this chapter and provide suggestions (and simple circuit diagrams if applicable) on how circuits can be altered to obtain improved performance. We will not go into detail on each of the circuits of chapter <a href="{{< ref "laser_driver.md#chapter-3.1" >}}">3.1</a> and chapter <a href="{{< ref "detector.md#chapter-4.1" >}}">4.1</a>, but instead focus on broader aspects and architectural decisions of the design that can be improved. 
We will start by discussing improvements to the current prototype, followed by a (small) discussion on how to prepare the different blocks in this system for an opto-electrical circuit realisation, which is the final goal of this project. 

# TEC Design

The prototype still has several issues, most notably the lack of real-time background subtraction and the large influence of the TEC. The first thing that should be added is a custom TEC circuit that is also controlled from the microcontroller. By using a digital implementation, the parameters and characteristics of the control loop can be more easily updated and altered for improved performance. Additionally, safe guards such as voltage and current control do not introduce undesirable non-linearities in the system. This is in contrast to an analogue implementation. First, we will discuss a possible implementation using op-amps. The usage of a dedicated chip might be more efficient though. We will briefly discuss this at the end of this section.

A TEC circuit consists of two parts: a measurement circuit that measures the thermistor resistance and a current source that provides cooling (or heating) to the laser based on the desired value of the thermistor and the actual value. Usually, lasers have a built-in thermistor of 10kΩ. The proposed design will assume that the laser has features such a thermistor.

A possible design for the sensing circuit is depicted in figure <a href="{{< ref "improvements.md#figure-7.2" >}}">7.2</a>. The current source principle from figure <a href="{{< ref "laser_driver.md#figure-3.16" >}}">3.16</a> is repeated here. The current source generates a fixed current of for instance 100μA that flows through the thermistor. This will set a voltage over the thermistor that is proportional to its resistance. Since the input current is known, the microcontroller can derive the thermistor value from this. Since most ADC have a non-negligible input current, a buffer circuit is added such that sampling does not affect the current flowing through the thermistor.

The measurement current depends on the thermistor range of interest. Using a 100μA current for instance, one is able to measure thermistor values from (approximately) 0Ω to 47.5kΩ when it is assumed that all amplifiers are input and output rail-to-rail and the supply voltage is 5V. This range will be too large for most applications (there is no need to go below 1 to 5kΩ for most applications). This will result in a value of up to 4.75V over the thermistor. A 0.25V buffer is need to ensure correct transistor behaviour and $R\_c$ must have the correct voltage drop to set the current. The desired voltage value can be fixed or variable. For most applications, a fixed sensing current should suffice. This can be set using a simple resistive divider. 

For correct operation, it is essential that the reference voltage remains fixed over time. A separate voltage rail can be constructed using an accurate voltage reference (for this circuit only). Otherwise, a calibration procedure will be needed at start-up where the system calibrates itself. This way, it can be guaranteed that the mean current tuning results in the wavelength corresponding to the centre of the absorption line. Other possibilities such as an etalon are also possible.

Do note that the circuit depicted in figure <a href="{{< ref "improvements.md#figure-7.2" >}}">7.2</a> does not provide any means to alleviate aliasing. An active Sallen-Key low-pass filter can be added to attenuate frequencies well above the Nyquist frequency. An example circuit can be found in figure <a href="{{< ref "detector.md#figure-4.19" >}}">4.19</a>. However, it was not included in the this TEC design since we do not expect the system to have many interferes at high frequencies. 

<figure id="figure-7.2">
<img alt="improvements:TEC_sensing" src="/images/TEC_sensing.svg">
<figcaption>
Figure 7.2: Sensing circuit to measure thermistor resistance. A current source sends a constant current through the thermistor. The current that arises over this thermistor is digitised using a single-end ADC.
</figcaption>
![](){#fig:}

Using the measured thermistor value from the circuit in figure <a href="{{< ref "improvements.md#figure-7.2" >}}">7.2</a>, a voltage source can be set to generate an input current that will either cool the laser down or heat it up. The current source again uses the same principle as the laser driver current source (figure <a href="{{< ref "improvements.md#figure-7.2" >}}">7.2</a>). However, the specifications of this circuit are different. 

To enable a fast response, the circuit should be able to provide a current of at least \pm 0.5A. Most lasers have a TEC rating in the range of 1 to 1.5A [<a href="{{< relref "#citation1" >}}">1</a>]. However, such high current values are usually not needed, loosening the design constraints on the transistor and resistor $R\_\text{c}$.  

Most lasers have good performance when they are below the ambient temperature (for instance the two <abbr title="Distributed Feedback (Laser)">DFB lasers we use in this dissertation need to be cooled. For cooling applications, a unidirectional current suffices. The circuit from figure <a href="{{< ref "improvements.md#figure-7.1" >}}">7.1</a> implements a unidirectional current source that can be used to cool the laser. The amplifier is assumed to be a rail-to-rail amplifier, if not, $R\_\text{c}$ needs to be tied to a voltage that is within the swing rang of the amplifier. 

The DAC should be able to have an output that equals the positive output rail voltage $V\_\text{CC}$. An example of such an DAC is the LTC1659 [<a href="{{< relref "#citation2" >}}">2</a>]. A custom built DAC, similar to the ones used in section <a href="{{< ref "laser_driver.md#section-3.1" >}}">3.1</a>, does not provide sufficient accuracy for this application with 1% resistor value.

<figure id="figure-7.1">
<img alt="improvements:TEC_current" src="/images/TEC_current.svg">
<figcaption>
Figure 7.1: TEC current source to cool down or heat up the laser to a fixed temperature.
</figcaption>
![](){#fig:}

The current source from figure <a href="{{< ref "improvements.md#figure-7.1" >}}">7.1</a> is a linear source, the output current is a linear function of the input voltage by setting a voltage over the resistor $R\_\text{c}$. Though the principle if the circuit in figure <a href="{{< ref "improvements.md#figure-7.1" >}}">7.1</a> is valid, it may not be practical. Suppose that we allow currents up to 1A and set $R\_\text{c}$ to 10Ω, then we have at most 10W dissipation by this resistor, which is not possible with normal resistors. Additionally, 10W of loss is a lot. A solution for this is a switched current supply. 

Switched current supplies such as NCP3065 from On Semiconductor can be used to achieve higher efficiency [<a href="{{< relref "#citation3" >}}">3</a>]. These supplies operate using a non-linear circuit which can generate distortion and and harmonics. However, since this source is used to tune the laser temperature, this should not form a real problem.

Yet another alternative is to use a dedicated TEC chip such as ADN8831 from Analogue Devices [<a href="{{< relref "#citation4" >}}">4</a>]. This device uses a reference voltage to set the temperature, which can come from a DAC that is controlled by the microcontroller. The feedback loop's parameters are set using discrete components at its terminals and should be optimised for ideal response that does not settle on any of the modulations at the laser input.

# Automatic Calibration

Lasers typically have a large dependence on their operating temperature, requiring accurate TEC control. The thermistor available for temperature measurement can however also have a certain tolerance and possibly vary over time. A calibration procedure is thus needed to tune the system such that the absorption peak's centre is at exactly the centre of the wavelength range.

During the measurements performed in chapter <a href="{{< ref "measurements_performance.md#chapter-6.1" >}}">6.1</a>, the correct alignment was manually verified and the TEC was updated accordingly. A calibration procedure can however alleviate the need to do this manually.

Usually, the designer of the set-up will have an idea of the correct thermistor value. This value can be used as first estimate. Then we propose to perform the algorithm from figure <a href="{{< ref "improvements.md#figure-7.3" >}}">7.3</a> that was specially designed for this application. This algorithm functions using three phases, depending on how sure it is of the optimal thermistor estimate. Using good design, the valid range for the thermistor value can be limited to a range that covers only one complete absorption peak. If this cannot be guaranteed, the system will lock on one of the absorption peaks, without knowing which one exactly.  

The operation can be explained as follows. We start the algorithm at its starting point during phase B. A predefined value is used as first estimate. This value can be the thermistor value from the previous measurement run (saved after shutdown) or from the designer estimate we discussed above. Since we are calibrating the system, we need to assume there is some measurable gas concentration already present in the gas cell, otherwise the procedure will fail. After performing a simple direct spectroscopy measurement, the system will determine that an absorption line is present in the scanned wavelength range if the dip $\Delta I\_\text{PD}$ is above a certain threshold $\rho\_I$. If so, than the thermistor value is updated with a new estimate (the value is increased or decreased using exponential decay) until it converges (the difference between the current and updated values is negligibly small; smaller than $\rho\_R$). Then the system enters phase C.

During phase C, the system validates the estimate from phase B. Both a direct absorption spectroscopy measurement and a WMS measurement are performed and their concentrations are compared. If these match sufficiently close (the concentration difference $\Delta C$ is smaller than a threshold $\rho\_C$), the algorithm stops. Otherwise it discards the estimate from B and starts phase A.

During phase A, the system linearly scans the thermistor value in steps of $\epsilon\_R$ from its minimum value to the maximum value. If the system has found an absorption peak, it enters phase B again. If it cannot find such a peak if and the thermistor value reaches $R\_\text{max}$, the procedure fails. The system can also enter phase A from phase B if no dip is found; and will perform a similar operation. If the system enters phase A multiple times, it is not restarted (since that might result in livelock), but continues from the last thermistor value that was used during this phase.

<figure id="figure-7.3">
<img alt="improvements:calibration" src="/images/calibration.svg">
<figcaption>
Figure 7.3: Calibration algorithm to ensure that the absorption peak is well centred.
</figcaption>
![](){#fig:}

# Improved Background Subtraction

In subsection <a href="{{< ref "laser_driver.md#subsection-3.5" >}}">3.5</a> and subsection <a href="{{< ref "detector.md#subsection-4.2" >}}">4.2</a> the importance of background subtraction is highlighted. The current system does not offer real-time background subtraction, making accurate signal resolution more difficult. One can always use offline background subtraction, where the system is first measured without any gas concentration present in the gas cell. Then the expected system response can be measured and stored to subtract from the incoming signal. 

This method has its drawbacks, since it assumes the system to be time-invariant, which is not the case (many components have temperature dependent specifications). It also requires that the system is calibrated every time, which is inconvenient. 

This can be improved by adding a second detector chain. This chain contains exactly the same components as the actual detector chain, but the light is not passed through the gas cell in this case, but directly falls onto a PD. This can be done by using a power splitter in the laser. The laser can for instance direct 5% of its output power to a PD that is integrated into this laser. The output current from this PD will then be processed by a duplicate of the detector chain through which the other 95% of the light passes.

The above procedure creates several additional difficulties however: apart from the gain mismatch due to the power splitter and gas cell losses, the (electrical) gains should be the same. Furthermore, both receiver chains should ideally have exactly the same overall transfer function characteristic. To this end, a sensitivity analysis needs to be performed and components should have lower variability (e.g. 0.1% resistor values). 

A simpler technique is obtained by employing an optical switch that direct the light either to the gas cell or directly to a second PD. This alleviates the need for a second detector chain. The gain still needs to be calibrated (losses will be different), but this was also the case for the previous architecture, since both detector chains are not perfect not the power splitter. 

# Scaling the System for On-chip Gas Sensing

The end goal of this system is to scale it to an on-chip measurement system (for the analogue part). Several opportunities and challenges arise however. We will briefly discuss these in this section.

First, an on-chip laser needs to be designed for this application around the required wavelength. This laser device should be optimally tuned for emission around one spectral line and low temperature dependence, alleviating possible TEC issues. The light from this laser can be split (using a $5/95$ split for instance) to obtain two signals: the weak one for background subtraction and the strong one for gas sensing.

The light will typically interact with the gas in the gas cell through an evanescent field when passing through a spiral structure. Such a structure is depicted in figure <a href="{{< ref "detector.md#figure-4.22" >}}">4.22</a>. This will require a more sensitive integrated detector, since the optical path length is smaller than our free space measurements and the amount of interaction is limited compared to a free space measurement. The 2f component might be too weak to detect using the same electrical signal path as the 1f component. A separate 1f and 2f path may be needed, as depicted in the alternative architecture from figure <a href="{{< ref "system.md#figure-2.1" >}}">2.1</a>.

The wavelength calibration can be considerably simplified compared to the large algorithm in figure <a href="{{< ref "improvements.md#figure-7.3" >}}">7.3</a>. An etalon can be added to the laser. During calibration, the light can be directed towards this structure to verify that the laser temperature is correct. The resonant wavelength should be the centre of the absorption peak. A shift of the resonance wavelength is possible due to process and temperature variations is still possible though.

The redesign of this system to a single integrated circuit requires that each of the amplifier circuits is replaced by an equivalent transistor design. These transistor levels designs will typically have lower relative tolerances on components, making the construction of a dedicated subtraction chain easier. The absolute tolerances will be higher typically, making the filter design more difficult. An alternative can be to use external components to set the active filters.
