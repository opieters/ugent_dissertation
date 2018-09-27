---
title: Detector System
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 4
---

In this chapter, we will discuss the design, constraints, and performance of the detector circuit. A high-level overview of the architecture can be found in figure <a href="{{< ref "detector.md#figure-4.14" >}}">4.14</a>. The chosen architecture attempts to have a good balance between complexity and performance. After an assessment of the power budget in section <a href="{{< ref "detector.md#section-4.2" >}}">4.2</a>, the required number of bits of the ADC is discussed and finally we discuss the design of each component.

Figure <a href="{{< ref "detector.md#figure-4.14" >}}">4.14</a> depicts the detector architecture. The light is captured and converted to a current signal by means of a PD. This current signal is subsequently converted and amplified to a voltage signal by means of a TIA. This voltage signal is then further manipulated by means of a high-pass filter. This filter removes undesired components (DC and triangle signals) from the incoming signal, while not altering the first and second order harmonics of the sinusoidal modulation. Additional amplification is provided by means of a VGA. Then the signal is digitised using an ADC, after low-pass filtering to prevent aliasing.

<figure id="figure-4.14">
<img alt="detector:architecture" src="/images/architecture.svg">
<figcaption>
Figure 4.14: Detector architecture overview. Light signal is detected, amplified and filtered before it is converted into a digital signal at the ADC.
</figcaption>
![](){#fig:}

# Power Budget Calculation

We will encounter different optical set-ups. These set-ups each have different attenuation and propagation characteristics. It is thus fundamental to have a good understanding of the attenuation in the entire system in order to have the best possible amplification to achieve a good SNR. Here, we will discuss the different set-ups and give an overview of typical, minimal and maximal attenuations we can expect. Throughout this discussion, lenses are considered lossless elements. Typical lenses for these applications have reflectances in the range of 0.1 to 1.4%. We will use the F810SMA-2000 lens from Thorlabs for the free space measurements, which has a reflectance of 1.3% [<a href="{{< relref "#citation1" >}}">1</a>, <a href="{{< relref "#citation2" >}}">2</a>].

Free space measurements by means of a lens and mirror system is the first set-up we will consider. An overview of this set-up along with expected attenuations is depicted in figure <a href="{{< ref "detector.md#figure-4.18" >}}">4.18</a>. Here, the main source of attenuation is the beam width at the output of the lens. This collimated beam has a diameter $w$ of 7.3mm at the lens output and a divergence angle of 0.02° [<a href="{{< relref "#citation1" >}}">1</a>]. The sensor on the detector side only as a diameter of 1mm while the optical path length is approximately 3.5m. The Rayleigh range of this beam is 20.9m, thus we can safely assume that the beam has not diverged when falling onto the PD. We assume that the centre of the PD is aligned to the centre of the Gaussian beam profile. In this case, the total power that falls on the PD is given by:

<div>
$$
P( r ) = P\_0\sbr{1-\exp\br{-\dfrac{2r^2}{w^2}}}
$$
</div>

Here, $r=0.5mm$ and $w(z)=7.3mm$, resulting in: ${P = 0.0369P\_0}$ or a loss of 14.3dB.

<figure id="figure-4.18">
<img alt="detector:lens_system_attenuation" src="/images/lens_system_attenuation.svg">
<figcaption>
Figure 4.18: Expected attenuation of an optical system in a lens-based free space measurement with a path length $L$ of approximately 3.5m.
</figcaption>
![](){#fig:}

The second and third set-ups are very similar. Both set-ups have the light wave interact using the evanescent field of the light wave on a photonic chip. Light does not directly propagate through the gas, but will propagate in a spiral waveguide and interact with surrounding gasses by means of the evanescent field. This principle and an example spiral structure for a glucose application is depicted in figure <a href="{{< ref "detector.md#figure-4.22" >}}">4.22</a> [<a href="{{< relref "#citation3" >}}">3</a>]. The main difference between both set-ups is how the signal is directed towards the PD. In the set-up from figure <a href="{{< ref "detector.md#figure-4.24" >}}">4.24</a>, the light is coupled into an optical fibre before falling onto the PD. This results in an additional loss due to inefficient coupling of light from the grating into the optical fibre. To avoid these losses, the PD can be placed directly above the grating, requiring a more complex vertical set-up and very good alignment between the grating and the photodetector. The overall losses with this last set-up (figure <a href="{{< ref "detector.md#figure-4.23" >}}">4.23</a>) vary between 16.4dB and 39dB compared to the set-up of figure <a href="{{< ref "detector.md#figure-4.24" >}}">4.24</a>, where losses range between 10.4dB and 29dB. A summary is provided in table <a href="{{< ref "detector.md#table-4.5" >}}">4.5</a>.

For all set-ups, we will use the same InGaAs PD from Hamamatsu. This is a broadband PD that works from 1.0 to 2.6μm. This PD also has a large photosensitive area with a diameter of 1mm. The cut-off frequency is at least 2MHz with a 50Ω load, which is more than sufficient for the application considered here [<a href="{{< relref "#citation4" >}}">4</a>].

<figure id="figure-4.22">
<img alt="detector:spiral" src="/images/spiral.svg">
<figcaption>
Figure 4.22: Working principle of on-chip measurement using the evanescent field and a spiral. Figure taken from [<a href="{{< relref "#citation3" >}}">3</a>].
</figcaption>
![](){#fig:}

<figure id="figure-4.24">
<img alt="detector:spiral_system_attenuation_2_gratings" src="/images/spiral_system_attenuation_2_gratings.svg">
<figcaption>
Figure 4.24: Expected attenuation of optical system in on-chip system 1: the signal is coupled into and out of the silicon chip using gratings, resulting in high loses. Additionally, there are losses inside the spiral. Only 20 to 80% of the signal interacts with air surrounding the spiral. This effectively attenuates the signal portion.
</figcaption>
![](){#fig:}

<figure id="figure-4.23">
<img alt="detector:spiral_system_attenuation_1_grating" src="/images/spiral_system_attenuation_1_grating.svg">
<figcaption>
Figure 4.23: Similar set-up as figure <a href="{{< ref "detector.md#figure-4.24" >}}">4.24</a>, but the  light coming out of the chip is not coupled into a fibre but falls directly on the PD. Very good alignment between PD and grating is required.
</figcaption>
![](){#fig:}

<table id="table-4.5">
<caption>Table 4.5: Optical loss overview in different set-ups.</caption>
<tr>
<th>set-up</th>
<th>min. loss (dB)</th>
<th>typ. loss (dB)</th>
<th>max. loss (dB)</th>
</tr>
<tr>
<td>free space</td>
<td>14.3</td>
<td>14.3</td>
<td>--</td>
</tr>
<tr>
<td>PD not aligned</td>
<td>16.4</td>
<td>23</td>
<td>39</td>
</tr>
<tr>
<td>PD aligned</td>
<td>10.4</td>
<td>15</td>
<td>29</td>
</tr>
</table>

Initially, we will focus on designing a system for the first two cases since these are easiest to measure in the lab. Especially the free space measurement, since this does not require probes and the set-up can be moved easily if needed.

# Detector Simulation

Before starting the detector design, we will perform some simulations to get an idea of typical signal amplitudes at the detector (receiver) end. Furthermore, we will take a look at the number of bits required in the digital domain for sufficient accuracy.

## Ideal Detector

The simulation model includes most components from the receiver chain. A schematic representation is depicted in figure <a href="{{< ref "detector.md#figure-4.21" >}}">4.21</a>. In this model, we can vary the bit depth (resolution) of the ADC along with the concentration of the gas in the gas cell. We will only perform simulations for theVCSEL laser, since the others are similar and we focus on the  ADC in this chapter. 

<figure id="figure-4.21">
<img alt="detector:simulation_model" src="/images/simulation_model.svg">
<figcaption>
Figure 4.21: Overview of simulation components to compare different signal generation techniques.
</figcaption>
![](){#fig:}

The output of this simulation is the ratio $R$ of the 2f component to the 1f component. An example simulation for an optical path length of 100cm and a CO~2~ concentration of 410ppm is depicted in figure <a href="{{< ref "detector.md#figure-4.25" >}}">4.25</a>. The ideal $R$ ratio is completely computed using floating point values and with perfect input (i.e. no quantisation noise). The non-ideal $R$ ratio includes ADC conversion. During this simulation, a VGA is used to scale the signal to the optimal level (the maximum amplitude of the signal is equal to the reference voltage). To simulate a realistic setting, we remain 0.3V from the maximum ADC amplitude of 3.3V.

<figure id="figure-4.25">
<img alt="detector:typical_simulation" src="/images/typical_simulation.svg">
<figcaption>
Figure 4.25: Typical second order harmonic to the first order harmonic ratio at detector side (simulated). The ideal and actual $R$ ratios overlap, indicating that there is negligible influence of the ADC noise.
</figcaption>
![](){#fig:}

<figure id="figure-4.26">
<img alt="detector:typical_simulation_1f_2f" src="/images/typical_simulation_1f_2f.svg">
<figcaption>
Figure 4.26: 1f and 2f components used to obtain the actual ratio $R$ from figure <a href="{{< ref "detector.md#figure-4.25" >}}">4.25</a>.
</figcaption>
![](){#fig:}

## Required Resolution for Accurate Measurements

The required resolution (bit depth) of the signal is an important characteristic, since this will determine whether a single-ended to differential amplifier will be needed. The internal ADC is a single-ended ADC, while most external ADC are differential ADC.

To simulate only the effect of the ADC, we will use noiseless components in the simulation and plot the second order harmonic to the first order harmonic ratio, since this is the quantity from which we will derive the concentration. Figure <a href="{{< ref "detector.md#figure-4.1" >}}">4.1</a> and Figure <a href="{{< ref "detector.md#figure-4.2" >}}">4.2</a> provide ratios for a concentration of 30ppm and an optical length of 1cm for a 12bit and 16bit ADC respectively. These conditions can occur for low concentration detection on a photonics chip. Background subtraction was applied, otherwise we cannot detect concentration levels this low.

In figure <a href="{{< ref "detector.md#figure-4.1" >}}">4.1</a> and figure <a href="{{< ref "detector.md#figure-4.2" >}}">4.2</a>, $R$ is the simulated ratio using a finite bit ADC (12bit and 16bit here). $R$ ideal on the other hand does not use an ADC and performs all computations using floating point arithmetic.

From these figures, it is clear that the 16bit ADC has better performance for CO~2~ concentrations that are one order in magnitude lower than ambient concentration of 410ppm. We will thus design a system with a 16bit ADC such that the ADC cannot form a limiting factor for accuracy. As a consequence, we will need to add a single-end to differential amplifier since most external ADC are differential.

<figure id="figure-4.1">
<img alt="detector:ADC_R_12_bit" src="/images/ADC_R_12_bit.svg">
<figcaption>
Figure 4.1: Second order harmonic to the first order harmonic ratio at detector side (simulated) for a 12bit ADC with background subtraction for a 30ppm concentration and a path length of 1cm.
</figcaption>
![](){#fig:}

<figure id="figure-4.2">
<img alt="detector:ADC_R_16_bit" src="/images/ADC_R_16_bit.svg">
<figcaption>
Figure 4.2: Second order harmonic to the first order harmonic ratio at detector side (simulated) for a 16bit ADC with background subtraction for a 30ppm concentration and a path length of 1cm.
</figcaption>
![](){#fig:}

# Transimpedance Amplifier

The TIA will convert the photocurrent from the PD into an amplified voltage signal. Sufficient amplification is required in this stage in order to be able to have less stringent noise specifications for subsequent stages. The noise added to the signal by this circuit is of fundamental importance and must be kept as low as possible, especially for the very weak 2f component. Noise outside the signal bands will be removed by the LIA (see chapter <a href="{{< ref "digital_control_processing.md#chapter-5.1" >}}">5.1</a>).

Several TIA design schematics were considered. Of these, the simple shunt-feedback resistor topology was selected because of its good tradeoff between noise, stability and simplicity. 

<h3 id="subsection-4.1">Circuit Analysis

In this subsection we will analyse the characteristics of the shunt-feedback resistor TIA circuit from figure <a href="{{< ref "detector.md#figure-4.8" >}}">4.8</a> and discuss limitations, stability and transimpedance of this circuit. A phase margin of at least 60° is required as well as a tolerance of 5% on individual components. 

<figure id="figure-4.8">
<img alt="detector:TIA_basic" src="/images/TIA_basic.svg">
<figcaption>
Figure 4.8: Simple TIA design. Final component values for $R\_\text{F}$ and $C\_\text{F}$ are listed in table <a href="{{< ref "detector.md#table-4.2" >}}">4.2</a>. The PD is modelled by its shunt resistance and terminal capacitance: $R\_\text{PD}=14kΩ$ and $C\_\text{PD}=500pF$.
</figcaption>
![](){#fig:}

The TIA circuit must be stable, otherwise we cannot use it to amplify the current signal from the PD. Hereto, we will first consider a simple op-amp model with one pole and stabilise  this circuit. Afterwards we will focus on simulations to further optimise the feedback capacitance and verify the phase margin. 

The gain $A(s)$ will be approximated by equation <a href="{{< ref "detector.md#equation-4.12" >}}">4.12</a> and the feedback factor $\beta$ is given by equation <a href="{{< ref "detector.md#equation-4.4" >}}">4.4</a>.

<div>
$$
A(s) = \dfrac{A\_0}{1+\tau\_A s},\qquad\tau\_A = \dfrac{1}{\omega\_A}
\tag{4.12}
$$
</div>

<div>
$$
\beta = \dfrac{Z\_1}{Z\_1+Z\_2}, \qquad Z\_1=R\_\text{PD}\parallel\dfrac{1}{s C\_\text{PD}}, Z\_2=R\_\text{F}\parallel\dfrac{1}{s C\_\text{F}}
\tag{4.4}
$$
</div>

This results in the following loop-gain:

<div>
$$
LG(s) = \dfrac{1+s C\_\text{F} R\_\text{F}}{1+\dfrac{R\_\text{F}}{R\_\text{PD}} + s \br{C\_\text{F} + C\_\text{PD}} R\_\text{F}} \dfrac{A\_0}{1+\tau\_A s}
\tag{4.2}
$$
</div>

The $LG$ formula in equation <a href="{{< ref "detector.md#equation-4.2" >}}">4.2</a> from has two poles and one zero, their corresponding frequencies are given below:

<div>
$$
\begin{align}
f\_1 &= \dfrac{1}{2\pi\tau\_A} && \text{(pole)} \\
f\_2 &= \dfrac{1}{2\pi C\_\text{F}R\_\text{F}} && \text{(zero)} \\
f\_3 &= \dfrac{1+\dfrac{R\_\text{F}}{R\_\text{PD}}}{2\pi \br{C\_\text{F}+C\_\text{PD}}R\_\text{F}} && \text{(pole)}
\end{align}
$$
</div>
 
The photodetector's equivalent circuit has the following component values: $R\_\text{PD} = 14kΩ$ and $C\_\text{PD}=500pF$ [<a href="{{< relref "#citation4" >}}">4</a>]. 

Before selecting appropriate components for the circuit, we need to select an appropriate amplifier. This amplifier should have very good noise performance and sufficient bandwidth. A comparison between different amplifiers is made based on the input referred noise current density. Equation <a href="{{< ref "detector.md#equation-4.3" >}}">4.3</a> provides an approximating formula. The resulting input referred noise density is depicted in figure <a href="{{< ref "detector.md#figure-4.7" >}}">4.7</a>. Other amplifiers that have even better noise performance using the metric from this figure (such as OPAx380 and LT1028) were also considered, but designs using these amplifiers failed to provide sufficient phase margin in the simulations performed with LTSpice (we require at least 60°), and were thus removed from the selection.

<figure id="figure-4.7">
<img alt="detector:TIA_amplifier_selection" src="/images/TIA_amplifier_selection.svg">
<figcaption>
Figure 4.7: TIA amplifier selection using input referred noise current density versus bandwidth of the system [<a href="{{< relref "#citation5" >}}">5</a>, <a href="{{< relref "#citation6" >}}">6</a>, <a href="{{< relref "#citation7" >}}">7</a>].
</figcaption>
![](){#fig:}

The following formula that estimates the input referred noise current density [<a href="{{< relref "#citation8" >}}">8</a>, <a href="{{< relref "#citation9" >}}">9</a>]:

<div>
$$
i\_\text{n, total} = \sqrt{i\_\text{n}^2+\br{\dfrac{e\_\text{n}}{R\_\text{F}}}^2 + \dfrac{4kT}{R\_\text{F}} + \dfrac{\br{2\pi e\_\text{n} f\_\text{-3 dB} C\_\text{in}}}{3}} \tag{4.3}
$$
</div>

In equation <a href="{{< ref "detector.md#equation-4.3" >}}">4.3</a>, $i\_\text{n}$ is the inverting input spot noise current density, $e\_\text{n}$ is the inverting input spot noise voltage density, $C\_\text{in}$ is the total input capacitance (it is assumed that this is dominated by the large photodiode capacitance) and $f\_\text{-3 dB}$ the noise integration frequency limit. All of these parameters can be found in the datasheet or can be calculated from the specifications/datasheet.

Since we use a sine wave of 10kHz, the highest signal frequency in our system is 20kHz. As a consequence, we select the LT6230 amplifier since this amplifier has the best noise performance for bandwidths below 1MHz, which is more than sufficient. The actual bandwidth of the final design is depicted in figure <a href="{{< ref "detector.md#figure-4.9" >}}">4.9</a> (with LTSpice op-amp model and real component values).

Next, we need to size the different components and select an appropriate amplifier. Optimal component sizing will depend upon the set-up used in the system. We will give a detailed explanation for theVCSEL. A similar reasoning can be made for the other two lasers.

From table <a href="{{< ref "laser_driver.md#table-3.5" >}}">3.5</a>, we can conclude that -- in the case of a VCSEL -- the maximal laser input current is 10.440mA. This results in an output power of 383μW (-4.2dBm). With a lowest typical attenuation of table <a href="{{< ref "detector.md#table-4.5" >}}">4.5</a>, this will result in a full-scale signal of -18.5dBm at the input of the PD. Taking the PD's spectral response of 1.25A/W into account, the input signal of the TIA has an amplitude of at most 1.77μA (this includes the bias and the triangle wave). We want to convert this current signal to a voltage signal with a maximum amplitude of approximately 3.0V. The ADC reference voltage is set to 3.3V. This is sufficiently far from the positive rail for all amplifiers in the detector chain [<a href="{{< relref "#citation10" >}}">10</a>, <a href="{{< relref "#citation11" >}}">11</a>, <a href="{{< relref "#citation12" >}}">12</a>]. We use a margin 0.3V such that the ADC does not introduce any non-linearity due to overloading it. This requires an amplification of 187kΩ. If we allow up to 5% tolerance on the transimpedance resistor, we can use the E24-series value of 180kΩ.

Typical values for the shunt-feedback resistor and parasitic capacitance of the PD are 14kΩ and 500pF, respectively [<a href="{{< relref "#citation4" >}}">4</a>]. We will design for the typical values of these parasitics, later we will also verify performance with the minimally guaranteed values.

Assuming there is no feedback capacitance ($C\_\text{F}=0$), the frequencies become:

<div>
$$
\begin{align}
f\_1 = 2kHz\qquad f\_3 = 24.5kHz\end{align}
$$
</div>

Clearly, the circuit will not have sufficient phase margin because the two phase shifts occur close to each other and the large loop gain. By adding a zero to the feedback path, we can compensate this and increase the phase margin such that the open loop phase crosses -180° when the gain is well below 0dB. However, this also reduces the bandwidth of the circuit. In this quick analysis, we want to have a phase margin of approximately 60°.

The analysis to obtain this minimal value for $C\_\text{F}$ was done numerically and resulted in a value of 21pF. This is a good starting point for our simulations.

After careful simulation, the following set of values was selected:

<div>
$$
\begin{align}
C\_\text{F} &= 18pF \\
R\_\text{F} &= 180kΩ
\end{align}
$$
</div>

A summary is provided in table <a href="{{< ref "detector.md#table-4.2" >}}">4.2</a> for theVCSEL and >DFB lasers. The transimpedance for the >DFB laser for CO is the same as that for theVCSEL laser, even though less power can be extracted due to the inefficient coupling from the chip to a fibre. If the transimpedance is increased, the circuit has insufficient bandwidth. Subsequent stages will have to further amplify the signal to suitable levels for digital conversion.

<table id="table-4.2">
<caption>Table 4.2: TIA feedback resistor and capacitor values and phase margin.</caption>
<tr>
<th>laser</th>
<th>$R\_\text{F}$ [kΩ]</th>
<th>$C\_\text{F}$ [pF]</th>
</tr>
<tr>
<td>VCSEL</td>
<td>180</td>
<td>18</td>
</tr>
<tr>
<td>>DFB CO~2~</td>
<td>68</td>
<td>33</td>
</tr>
<tr>
<td>>DFB CO</td>
<td>180</td>
<td>18</td>
</tr>
</table>

The resulting bandwidth and phase margins are discussed in the next section, along with the noise performance of the entire circuit. 

## Circuit Performance

The circuit has a phase margin of at least 60°, as discussed in the previous section. The phase margins for the different circuits are listed in table <a href="{{< ref "detector.md#table-4.1" >}}">4.1</a>.
<table id="table-4.1">
<caption>Table 4.1: Important characteristics TIA circuit: bandwidth, phase margin and input current noise.</caption>
<tr>
<th>}
laser</th>
<th>bandwidth [kHz]</th>
<th>phase margin [°]</th>
<th>noise at second order harmonic [pA]</th>
</tr>
<tr>
<td>VCSEL</td>
<td>48.9</td>
<td>69.2</td>
<td>2.68</td>
</tr>
<tr>
<td>>DFB CO~2~</td>
<td>70.8</td>
<td>61.4</td>
<td>2.71</td>
</tr>
<tr>
<td>>DFB CO</td>
<td>48.9</td>
<td>69.2</td>
<td>2.68</td>
</tr>
</table>


The gain plot and input noise current density plots are depicted in figure <a href="{{< ref "detector.md#figure-4.9" >}}">4.9</a> and figure <a href="{{< ref "detector.md#figure-4.10" >}}">4.10</a> respectively. These gain value are indeed the expected transimpedance values from table <a href="{{< ref "detector.md#table-4.2" >}}">4.2</a>. The provided bandwidth suffices for the application considered where we need to detect a 10kHz and 20kHz signal. 

<figure id="figure-4.9">
<img alt="detector:TIA_gain" src="/images/TIA_gain.svg">
<figcaption>
Figure 4.9: Bode plot of the transfer function from TIA output to TIA input.
</figcaption>
![](){#fig:}

The input noise current density is minimal for the two signals of interest, as observed in figure <a href="{{< ref "detector.md#figure-4.10" >}}">4.10</a> We can thus expect similar SNR degradation for both the 1f and 2f signals. Also notice that the input noise current density was estimated correctly in figure <a href="{{< ref "detector.md#figure-4.7" >}}">4.7</a> in our region of interest, indicating that the approximations made are valid.

<figure id="figure-4.10">
<img alt="detector:TIA_noise" src="/images/TIA_noise.svg">
<figcaption>
Figure 4.10: Voltage noise density at the output of the TIA.
</figcaption>
![](){#fig:}

<h2 id="section-4.1">Filter Design

In this section, we will discuss the design of two active filters using the Sallen-Key topology. Active filters are employed due to the low frequencies in the system, making passive filters impractical.

First a high-pass filter design is discussed that eliminates undesired components from the output signal of the TIA (the DC and triangle signals). The low-pass filter is used just before the ADC and reduces the amplitude of high frequency components and prevent possible aliasing by these components. 

<h3 id="subsection-4.3">High-pass Filter

The circuit diagram of  a second order high-pass filter using the Sallen-Key topology is depicted in figure <a href="{{< ref "detector.md#figure-4.16" >}}">4.16</a>. The transfer function and filter characteristics are listed in equation <a href="{{< ref "detector.md#equation-4.8" >}}">4.8</a>, equation <a href="{{< ref "detector.md#equation-4.6" >}}">4.6</a> and equation <a href="{{< ref "detector.md#equation-4.7" >}}">4.7</a> [<a href="{{< relref "#citation13" >}}">13</a>]. 

<div>
$$
\begin{align}
\dfrac{V\_\text{out}}{V\_\text{in}} = H\_\text{HP}(s) &= \dfrac{s^2 C\_1 C\_2 R\_1 R\_2}{1 + s R\_1 \br{C\_1+C\_2} + s^2 C\_1 C\_2 R\_1 R\_2 } \tag{4.8} \\
f\_\text{c} &= \dfrac{1}{2\pi\sqrt{C\_1 C\_2 R\_1 R\_2}} \tag{4.6}\\
\dfrac{2\pi f\_\text{c}}{Q} &= \dfrac{1}{R\_1 \br{C\_1 + C\_2}}
\tag{4.7}
\end{align}
$$
</div>

We will design a Butterworth filter. This filter has a maximally flat transfer function and stronger roll-off than a similar Bessel filter. This translates to a requirement in a quality factor $Q$ of $\sfrac{1}{\sqrt{2}}$. The cut-off frequency will be set to 1kHz. This results in a negligible attenuation at our signal frequencies and sufficient attenuation for the low frequency triangle wave. Since the input is connected to the output of our TIA (which is a very low op-amp output impedance), there is no need to take the source impedance into account. The components chosen to realise this filter are listed in table <a href="{{< ref "detector.md#table-4.4" >}}">4.4</a>.

<figure id="figure-4.16">
<img alt="detector:high_pass_butter" src="/images/high_pass_butter.svg">
<figcaption>
Figure 4.16: High-pass active Butterworth filter design using the Sallen-Key topology [<a href="{{< relref "#citation13" >}}">13</a>].
</figcaption>
![](){#fig:}

We must also ensure that the input impedance of this circuit remains sufficiently high such that we do not overload the TIA. The input impedance is:

<div>
$$

Z\_\text{in, HP} = \dfrac{1 + s R\_1 \br{C\_1+C\_2}+ s^2 C\_1 C\_2 R\_1 R\_2 }{s C\_1 + s^2 C\_1 C\_2 R\_1} \tag{4.1}

$$
</div>

For low frequencies, equation <a href="{{< ref "detector.md#equation-4.1" >}}">4.1</a> can be simplified to $\sfrac{1}{sC\_1}$ and for high frequencies to $R\_2$. The low frequency impedance will be very large while the high-frequency input impedance is sufficiently high such that we do not expect any issues with the TIA op-amp driving this stage. The input impedance for the selected component values is depicted in figure <a href="{{< ref "detector.md#figure-4.17" >}}">4.17</a>.

<figure id="figure-4.17">
<img alt="detector:input_impedance_filters" src="/images/input_impedance_filters.svg">
<figcaption>
Figure 4.17: Input impedance of high-pass and low-pass Sallen-Key filters.
</figcaption>
![](){#fig:}

We will use the OPA277 amplifier from Texas Instruments (TI) as operational amplifier together with the following component values from table <a href="{{< ref "detector.md#table-4.4" >}}">4.4</a>. The OPA277 was selected because it is a precision amplifier with low offset voltage (10μV at most), good phase margin (it is unity-gain stable) and low-noise operation [<a href="{{< relref "#citation10" >}}">10</a>]. These values result in a cut-off frequency of 1001.4Hz (this value is obtained from the simulation). 
<table id="table-4.4">
<caption>Table 4.4: Component values for active high- and low-pass filter structures.</caption>
<tr>
<th></th>
<th>high-pass filter</th>
<th>low-pass filter</th>
</tr>
<tr>
<td>$R\_1$</td>
<td>7.5kΩ</td>
<td>1.5kΩ</td>
</tr>
<tr>
<td>$R\_2$</td>
<td>15kΩ</td>
<td>1.5kΩ</td>
</tr>
<tr>
<td>$C\_1$</td>
<td>15nF</td>
<td>150pF</td>
</tr>
<tr>
<td>$C\_2$</td>
<td>15nF</td>
</table>

<figure id="figure-4.5">
<img alt="detector:HP_filter_gain" src="/images/HP_filter_gain.svg">
<figcaption>
Figure 4.5: Bode magnitude plot of high-pass filter gain.
</figcaption>
![](){#fig:}

<figure id="figure-4.6">
<img alt="detector:LP_filter_gain" src="/images/LP_filter_gain.svg">
<figcaption>
Figure 4.6: Bode magnitude plot of low-pass filter gain.
</figcaption>
![](){#fig:}

The stability of figure <a href="{{< ref "detector.md#figure-4.16" >}}">4.16</a> cannot be investigated simply by cutting the feedback at the inverting input clamp of the amplifier, since there is also positive feedback through $R\_1$. As a result, the GFT is applied in LTSpice. The GFT is a universal method that can be applied to any circuit [<a href="{{< relref "#citation14" >}}">14</a>, <a href="{{< relref "#citation15" >}}">15</a>]. The GFT simulation results indicate a phase margin specification of 63.0°.

## Low-pass Filter

A very similar reasoning can be made for the low-pass filter as in subsection <a href="{{< ref "detector.md#subsection-4.3" >}}">4.3</a>. Again, we employ the Sallen-Key filter topology. Figure <a href="{{< ref "detector.md#figure-4.16" >}}">4.16</a> can easily be converted into a low-pass filter by replacing all capacitors by resistors and vice versa. The result is depicted in figure <a href="{{< ref "detector.md#figure-4.19" >}}">4.19</a>. 

The following transfer function, cut-off frequency and $Q$-factor are obtained:

<div>
$$
\begin{align}
\dfrac{V\_\text{out}}{V\_\text{in}} = H\_\text{LP} &= \dfrac{1}{1 + s C\_2 \br{R\_1+R\_2} + s^2 C\_1 C\_2 R\_1 R\_2 } \tag{4.9} \\
f\_\text{c} &= \dfrac{1}{2\pi\sqrt{C\_1 C\_2 R\_1 R\_2}} \\
\dfrac{2\pi f\_\text{c}}{Q} &= \dfrac{1}{C\_2 \br{R\_1 + R\_2}} \tag{4.10}
\end{align}
$$
</div>

<figure id="figure-4.19">
<img alt="detector:low_pass_butter_circuit" src="/images/low_pass_butter_circuit.svg">
<figcaption>
Figure 4.19: Low-pass active Butterworth filter design using the Sallen-Key topology [<a href="{{< relref "#citation13" >}}">13</a>].
</figcaption>
![](){#fig:}

We will opt to design a Butterworth filter, same as for the high-pass filter. This time with a cut-off frequency of 100kHz. The resulting component values are listed in table <a href="{{< ref "detector.md#table-4.4" >}}">4.4</a>. This cut-off frequency is too high to stop aliasing from occurring at frequencies between 40kHz and approximately 100kHz. However, selecting a lower value for the cut-off frequency results in attenuation of the second order harmonic, which is very undesirable since this signal is already very weak. Furthermore, it also results in a non-negligible gain mismatch between the first and second order harmonics. A higher-order filter structure where the roll-off is increased beyond 20kHz (but not at 20kHz) can solve this issue. However, required filter order will be higher, resulting in more power dissipation and complexity (several op-amps are needed for this).  

A Bode magnitude transfer function plot is depicted in figure <a href="{{< ref "detector.md#figure-4.6" >}}">4.6</a>, and indeed the cut-off frequency is approximately 100kHz (99.3kHz from the simulation). The same amplifier was used as in subsection <a href="{{< ref "detector.md#subsection-4.3" >}}">4.3</a>, since the requirements are similar. 

The input impedance should also not be an issue for this circuit, since the minimal input impedance is approximately $R\_1$ [<a href="{{< relref "#citation16" >}}">16</a>]. The exact input impedance is provided in equation <a href="{{< ref "detector.md#equation-4.11" >}}">4.11</a>.

<div>
$$

Z\_\text{in, LP} = \dfrac{1 + s C\_2 (R\_1+R\_2) + s^2 C\_1 C\_2 R\_1 R\_2}{s C\_2 + s^2 C\_1 C\_2 R\_2} 
\tag{4.11}

$$
</div>


Again, we need to apply the GFT to obtain the phase margin. For this circuit, a phase margin of 67.7° is achieved.

# Variable Gain Amplifier

As discussed in section <a href="{{< ref "detector.md#section-4.2" >}}">4.2</a>, we have several set-ups and lasers we need to accommodate. This can be done by building the same electronics several times, with different TIA and post-TIA amplification. However, a VGA is another option. By means of a VGA, a single circuit can be used for the different optical set-ups while providing sufficient gain levels depending upon the signals in the system.

A VGA has variable gain, depending on its configuration, which is determined by digital control logic from the microcontroller. As a result, the TIA gain can be fixed to a certain level to accommodate all set-ups.

First, we will discuss the circuit layout and chosen components, followed by the performance of this circuit in simulation and additional noise introduced by this gain stage.

<h3 id="subsection-4.1">Circuit Analysis

VGA exist as single package amplifiers that can be controlled directly using digital logic. However, we have chosen to make a custom design using an amplifier and a set of switches. The resulting circuit is depicted in figure <a href="{{< ref "detector.md#figure-4.11" >}}">4.11</a>. 

<figure id="figure-4.11">
<img alt="detector:VGA_circuit" src="/images/VGA_circuit.svg">
<figcaption>
Figure 4.11: Simplified circuit of VGA design.
</figcaption>
![](){#fig:}

<figure id="figure-4.20">
<img alt="detector:noninterting_op_amp" src="/images/noninterting_op_amp.svg">
<figcaption>
Figure 4.20: Non-inverting op-amp circuit.
</figcaption>
![](){#fig:}

The circuit in figure <a href="{{< ref "detector.md#figure-4.11" >}}">4.11</a> can be reduced to the non-inverting amplifier circuit from figure <a href="{{< ref "detector.md#figure-4.20" >}}">4.20</a>, where the two resistances have been split into several resistors with switches in between. By means of these switches, the resistive divider ratio from output to the negative amplifier clamp can be varied. This results in different levels of amplification. The amount of amplification is (the symbols refer to the resistance values in figure <a href="{{< ref "detector.md#figure-4.20" >}}">4.20</a>):

<div>
$$
v\_\text{out} = v\_\text{in} \br{1+\dfrac{R1}{R2}} \tag{4.5}
$$
</div>

The LT1222 amplifier is selected because of its large gain-bandwidth product (500MHz), low maximum input offset voltage (300μV at most) and very low output impedance at frequencies of interest (typically below 0.01Ω). Furthermore, a compensation capacitor can be added to reduce the bandwidth and thus stabilising the design more easily [<a href="{{< relref "#citation11" >}}">11</a>]. 

The ADG412 is a set of four analogue switches that will be used to vary the gain. This device features four CMOS switches with individual control and a low on-resistance of at most 35Ω. By placing the switch's on-resistance in series with the very high input impedance of the amplifier input clamps, we can neglect this resistance during design. This is favourable, since the exact resistance values are dependent on the drain/source voltage and supply voltage [<a href="{{< relref "#citation17" >}}">17</a>]. During switching between two lines, there might be a very short period of time when there is no feedback around the amplifier. To prevent this, a feedback capacitor $C$ of 20pF is used. This value is sufficiently large to hold the output voltage during switching [<a href="{{< relref "#citation18" >}}">18</a>].

For our design, we will use gain configurations of 0dB, 10dB, 20dB and 30dB, offering a sufficiently wide range for typical applications considered. Since these are set by selecting a set of four resistors, these can also be updated easily without the need to redesign the entire detector circuit.

To achieve a gain settings that approximate these values, we selected resistors of 1.1kΩ, 2.4kΩ, 7.5kΩ and 24kΩ for $R\_1$, $R\_2$, $R\_3$ and $R\_4$ respectively. This results in the following gain settings: 0dB, 10.05dB, 20.0dB and 30.05dB. We require resistor values with 1% accuracy here.

## Circuit Performance

To evaluate the performance of this circuit, we have depicted the gain plot in figure <a href="{{< ref "detector.md#figure-4.12" >}}">4.12</a>. To achieve sufficient phase stability, a 1nF compensation capacitance was added to the circuit at the compensation clamp of the amplifier. This capacitor reduces the amplifier's bandwidth, but this is not an issue for our maximum frequency of 20kHz, since the roll-off for 30dB gain in figure <a href="{{< ref "detector.md#figure-4.12" >}}">4.12</a> is still limited. We observe peaking in case of the 0dB configuration and a transition between first order and second order roll-off for the 10dB configuration. This peaking is not an issue, since it will be filtered by the low-pass filter before digital conversion. Removing this peak results in insufficient bandwidth for the 30dB gain configuration.

We required resistance values with a tolerance of at most 1%. From table <a href="{{< ref "detector.md#table-4.3" >}}">4.3</a>, it is clear that we can relatively accurately generate the gain levels this way.

By means of the compensation capacitor, we can also achieve a sufficiently large phase margin of at least 63.8° degrees (in the unity gain configuration) and more. A larger feedback capacitor can be used to further increase the phase margin at the cost of less gain. However, this is not needed here.

Due to the possibly large amplification, it is also important to have an idea on the noise added by this stage. Figure <a href="{{< ref "detector.md#figure-4.13" >}}">4.13</a> depicts the input noise voltage density of this circuit for the different gain configurations. The 0dB and 30dB settings have similar noise results, both dominated by the amplifier noise, while in the other configurations, the resistor noise is non-negligible.

<table id="table-4.3">
<caption>Table 4.3: Overview of VGA phase margin and gain deviation for each gain setting.</caption>
<tr>
<th>gain setting</th>
<th>phase margin</th>
<th>typ. gain [dB]</th>
<th>max. deviation [dB]</th>
</tr>
<tr>
<td>0dB</td>
<td>63.8°</td>
<td>0.0</td>
<td>0</td>
</tr>
<tr>
<td>10dB</td>
<td>90.4°</td>
<td>10.05</td>
<td>± 0.12</td>
</tr>
<tr>
<td>20dB</td>
<td>90.9°</td>
<td>20.0</td>
<td>± 0.16</td>
</tr>
<tr>
<td>30dB</td>
<td>90.6°</td>
<td>30.05</td>
<td>± 0.17</td>
</tr>
</table>

<figure id="figure-4.12">
<img alt="detector:VGA_gain" src="/images/VGA_gain.svg">
<figcaption>
Figure 4.12: VGA gain plot from input to output.
</figcaption>
![](){#fig:}

<figure id="figure-4.13">
<img alt="detector:VGA_noise" src="/images/VGA_noise.svg">
<figcaption>
Figure 4.13: VGA input referred noise voltage density.
</figcaption>
![](){#fig:}

# Analogue-to-Digital Converter Driver

The final part of the analogue detector front end is the single-end to differential converter and the ADC. A differential amplifier will convert the single-end output voltage of the low-pass filter into a differential voltage. This will then form the input of the ADC and converted into digital samples. 

We will use an external ADC since the internal ADC has only a resolution of 12bits and a resolution of 16bits is needed for improved accuracy. This was verified using simulations of the system in subsection <a href="{{< ref "detector.md#subsection-4.2" >}}">4.2</a>. 

The 16bit ADC we will use in our system, is the AD7687 from Analog Devices. This device can have a sample rate up to 250ksps, which is more than sufficient for the application considered here. Furthermore, it is able to sample voltages from $-V\_\text{ref}$ to $V\_\text{ref}$ from its differential inputs ($V\_\text{ref}$ is a fixed reference voltage). Each input however, can only vary in the range of 0 to $V\_\text{ref}$. As a consequence, the differential amplifier must also add a common mode voltage at its outputs such that these requirements are met [<a href="{{< relref "#citation19" >}}">19</a>]. In our application, we will use a reference voltage of 3.3V. This is sufficiently far from the differential rails (\pm 5V) such that we do not expect any non-linear behaviour by the amplifier [<a href="{{< relref "#citation12" >}}">12</a>].

The selected differential amplifier is the THS4130. This is a high speed device that is often used for this type of applications. It provides all of the necessary features for the application considered here [<a href="{{< relref "#citation12" >}}">12</a>]. 

The single-end to differential conversion circuit is depicted in figure <a href="{{< ref "detector.md#figure-4.3" >}}">4.3</a>. This is a simplified circuit, since the resistive divider that sets the common mode voltage at the differential amplifier is not included. The common mode voltage will be set to 1.65V, or half the reference voltage. We will not provide any gain in this stage, to this end, all resistors should be chosen equal: $R\_1=R\_2=R\_3=R\_4=R$ and we will fix $R$ at 5.1kΩ (with an error of 1%). The capacitors $C\_1$ and $C\_2$ are also chosen equally large at 330pF and provide low-pass filtering for high frequency components.

If the output impedance of the previous stage (source impedance in this stage) is not sufficiently low, $R\_1$ and $R\_3$ should be modified to accommodate this such that the gain for the positive and negative outputs remains equal. A good explanation on how to do this can be found in [<a href="{{< relref "#citation20" >}}">20</a>]. However, the OPA277 from the preceding low-pass filter has a low output resistance (typically 4Ω at 20kHz), within the resistor error margin of our resistor $R$. As a consequence, we do not need to take the output impedance of the previous stage into account. 

<figure id="figure-4.3">
<img alt="detector:ADC_driver" src="/images/ADC_driver.svg">
<figcaption>
Figure 4.3: ADC driver for single-end to differential signal conversion.
</figcaption>
![](){#fig:}

## Circuit Performance

We will evaluate the circuit based on the closed loop transfer function gain and phase margin. The gain from input to the differential output is depicted in figure <a href="{{< ref "detector.md#figure-4.4" >}}">4.4</a>. The roll-off is due to the addition of feedback capacitors in the circuit. As expected is the gain approximately 0dB for all frequencies of interest.

The open-loop phase margin of this circuit is 75.9°, which is more than sufficient.

<figure id="figure-4.4">
<img alt="detector:ADC_gain" src="/images/ADC_gain.svg">
<figcaption>
Figure 4.4: Bode magnitude plot of differential ADC driver gain.
</figcaption>
![](){#fig:}

# Conclusion

This chapter discussed the losses in the optical system in section <a href="{{< ref "detector.md#section-4.2" >}}">4.2</a>, looked at the amount of required bits and designed the different hardware blocks required in the detector. The photocurrent from the PD will be converted into a voltage signal using a TIA with optimal gain and bandwidth. The undesired DC and triangle signals are subsequently removed using an active high-pass filter. The signal is then further amplified using a digitally controlled VGA. Before sampling with a differential ADC, the signal is first low-pass filtered and converted to a differential signal.

These different hardware blocks each have their own transfer function and associated gain. The overall transfer function of the detector is depicted in figure <a href="{{< ref "detector.md#figure-4.15" >}}">4.15</a> for the CO~2~ >DFB laser and a VGA configuration of unity gain.

From this transfer function, we can conclude that the detector has the expected gain of 96.56dBΩ and 96.33dBΩ for the 1f and 2f components respectively. These values are very close to the ideal value of 68kΩ or 96.65dBΩ. Additionally, the gain offset between both components is very small (less than 0.5dB).

The bandwidth of this transfer function is also not too small such that we can slightly tune the sine frequencies if the need arises. We can also observe that the DC and triangle frequencies are strongly attenuated by the system compared to the 1f and 2f signals.

<figure id="figure-4.15">
<img alt="detector:full_tf" src="/images/full_tf.svg">
<figcaption>
Figure 4.15: Bode magnitude plot of the entire transfer function from the PD photocurrent to the ADC input after the single-ended to differential converter.
</figcaption>
![](){#fig:}
