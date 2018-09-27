---
title: Development of a Wavelength Modulation Spectroscopy System
date:   "2018-09-08"
tags: [Dissertation]
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
---



**Abstract** We propose a small-scale, cost effective system architecture for <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> measurements. <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> is a technique whereby gas absorption lines are used go generate harmonics of a due to a sinusoidal wavelength modulation. Higher order harmonics are generated due to the non-linear transmission function and are highly dependent on gas properties. Gas properties such as concentration, flow rate and temperature can be extracted using <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr>. We designed a system that consists of a laser driver and detector to scan over a gas absorption line and extract the gas concentration using the first and second order harmonics. A prototype has been built to measure <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> gas concentrations. Ambient levels of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> are measurable using this set-up within a 1s measurement interval. Future prototypes can build on this architecture to achieve increased performance. 

**Index Terms** <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr>, gas concentration measurement, <abbr title="Tuneable Diode Laser Absorption Spectroscopy">TDLAS</abbr>, <abbr title="Lock-In Amplifier">LIA</abbr>, <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr>, <abbr title="Carbon Monoxide">CO</abbr>, <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser, <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>, <abbr title="Thermoelectric Cooling">TEC</abbr>

<h2>Introduction</h2>

<abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> is a technique whereby gas properties such as gas concentration, flow rate and temperature can be extracted from light-matter interaction. Light emitted by a laser diode is modulated by means of a triangle and sine wave. The resulting laser line has a wavelength that follows these modulations and interacts with the gasses present in the environment. The triangle wave is a has a low frequency compared to the sine modulation and is intended to change the bias conditions such that the laser line scans the entire absorption peak. The sine modulation is used to generate higher order harmonics due to the non-linear transmission function. These higher order harmonics are extracted at the receiver to obtain the gas properties. 

<abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> is a a special case of <abbr title="Tuneable Diode Laser Absorption Spectroscopy">TDLAS</abbr>. In <abbr title="Tuneable Diode Laser Absorption Spectroscopy">TDLAS</abbr>, gas absorption lines are used to extract gas properties. Two forms of <abbr title="Tuneable Diode Laser Absorption Spectroscopy">TDLAS</abbr> exist: line scanning  absorption spectroscopy and <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr>. Line scanning  absorption spectroscopy is a simple technique where the laser's wavelength is linearly tuned over a gas absorption line using a ramp or triangle current modulation to drive the laser diode. The absorption line will result in a dip in the transmitted power, from which gas properties such as gas concentration and temperature can be extracted. A simulation of a line scanning  absorption spectroscopy measurement for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> around at 2003.5nm is depicted in figure <a href="{{< ref "#4" >}}">4</a>. 

The amount of power incident on the <abbr title="Photodiode">PD</abbr> is given by the law of Beer-Lambert:

<div>
$$

I\_\text{PD}(\lambda) = I\_0 \exp\br{-\alpha(\lambda) L}
\tag{1}

$$
</div>

where $I\_0$ is the output power of the laser. Ideally, this is independent of the wavelength. However, for most lasers this is not the case. Usually, when the input current is increased linearly, the output power also increases linearly, resulting in <abbr title="Residual Amplitude Modulation">RAM</abbr>. $\alpha(\lambda)$ is the absorption coefficient, wavelength dependent and proportional to the concentration of the gasses present. Finally, $L$ is the optical path length (the length over which the light can interact with the gas specimen). In the remainder, we will focus on concentration extraction.

Using the law from equation <a href="{{< ref "#1" >}}">1</a>, we can extract the concentration if both the output power of the laser and incident power on the <abbr title="Photodiode">PD</abbr> measured. Alternatively, if the output power cannot be measured directly, a non-absorbing region can be identified to obtain an estimate of the emitted power. For example, in figure <a href="{{< ref "#4" >}}">4</a>, such regions can be found near 0.5s and 0.55s where there is little absorption. A linear model can then be fitted to these regions to estimate the emitted laser power. However such regions do not always exist. <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> for instance has considerable absorption between absorption peaks in the 1990 to 2040nm range. A plot of the absorption coefficient in this region is depicted in figure <a href="{{< ref "#2" >}}">2</a>.



<figure id="figure-4">
<img alt="ext_abstr:direct-absorption-measurement" src="/images/direct-absorption-measurement.svg">
<figcaption>
Figure 4: Typical line scanning absorption spectroscopy measurement with and without <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> for a <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> laser (see table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a> for specifications) from 2002.3nm to 2002.8nm for a path length of 100cm and a <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration of 800ppm.
</figcaption>
</figure>



<abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> is an alternative technique that does not suffer from this drawback. In <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr>, the laser is tuned using two modulations: a slow linear modulation that scans over the absorption peak and a fast, small sinusoidal modulation. The slow modulation is used to change the biasing conditions of the system over time and thus scan over the absorption peak. The fast sinusoidal modulation only scans over part of the absorption peak. The <abbr title="First Order Harmonic">1f</abbr> amplitude (expressed as wavelengths) is ideally 2.2 times the <abbr title="Half-Width-Half-Maximum">HWHM</abbr> of the absorption peak [<a href="{{< relref "#citation1" >}}">1</a>], since this results in a maximal <abbr title="Second Order Harmonic">2f</abbr> amplitude. 






<figure id="figure-3">
<img alt="ext_abstr:architecture_block_diagram" src="/images/architecture_block_diagram.svg">
<figcaption>
Figure 3: System architecture block diagram.
</figcaption>
</figure>



The gas concentration is extracted as follows: the absorption line presents a a non-linear transmission function that will generate higher order harmonics of the applied sine wave (which is the first order harmonic). Since these harmonics are generated due to the absorption peak, their properties are highly dependent on the shape and strength of the absorption peak which in turn depends on the gas concentration. Higher order harmonics are very small and decaying with increasing frequency, so the second order harmonic is usually used [<a href="{{< relref "#citation2" >}}">2</a>]. The <abbr title="Second Order Harmonic">2f</abbr> component is then normalised using the <abbr title="First Order Harmonic">1f</abbr> component (which will also be deformed by the absorption line) to cancel the electro-optical gain. This way, there is no need to exactly know the emitted laser power. 



<figure id="figure-2">
<img alt="ext_abstr:absorption_coefficient_CO_2" src="/images/absorption_coefficient_CO_2.svg">
<figcaption>
Figure 2: Absorption peaks of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> between 1990nm and 2040nm at 296K (23C) for a 100% <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration at 1atm [<a href="{{< relref "#citation3" >}}">3</a>].
</figcaption>
</figure>



The typical shapes of the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components are depicted in figure <a href="{{< ref "#1" >}}">1</a> (two lower graphs). These are combined into a ratio $R\_{2/1}$ as follows [<a href="{{< relref "#citation2" >}}">2</a>] (upper right graph):

<div>
$$
\begin{align}
R\_{2/1}^2 =& \br{\br{\dfrac{I\_{2f}}{R\_{1f}}}\_\text{meas}-\br{\dfrac{I\_{2f}}{R\_{1f}}}\_\text{bg}}^2\\&+ \br{\br{\dfrac{Q\_{2f}}{R\_{1f}}}\_\text{meas} - \br{\dfrac{Q\_{2f}}{R\_{1f}}}\_\text{bg}}^2
\end{align}
$$
</div>
 



Here, the $I$ refers to the in-phase component and $Q$ the quadrature component, similar to the terminology used in transceiver systems. We need to use an in-phase and quadrature measurement technique to obtain a phase-shift independent measurement. A background signal (index bg) may be subtracted from the measured signal to take undesired non-idealities from the laser and electronics into account. The above $R\_{2/1}$ ratio assumes that the <abbr title="First Order Harmonic">1f</abbr> component does not approach zero, this is not an issue as long as the concentration remains sufficiently small. Otherwise the mean of the <abbr title="First Order Harmonic">1f</abbr> component must be used to rescale the <abbr title="First Order Harmonic">1f</abbr> component [<a href="{{< relref "#citation4" >}}">4</a>].



<figure id="figure-1">
<img alt="ext_abstr:1f_2f_LIA" src="/images/1f_2f_LIA.svg">
<figcaption>
Figure 1: Typical shape of a WMS spectroscopy measurements. A fast sinusoid is modulated (with modulation index of 2.2) on a slow ramp modulation that changes the bias conditions. The transmission power is depicted with and without <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> for a <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> laser (VL-2004-1-ST-H4) from 2002.3nm to 2002.8nm for a path length of 100cm and a <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration of 800ppm.
</figcaption>
</figure>




<h2>System Architecture</h2>

The architecture of our <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> system is depicted in figure <a href="{{< ref "#3" >}}">3</a> and can be subdivided into four parts: analogue/digital electronics (green blocks), optics (white blocks), digital control logic which implements several features in its programme code (blue blocks), and the computer (red block). This paper focusses on the electronics and digital part, a laser and photodetector are already provided. We will use the EP2004-DM-B <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser from Eblana Photonics and the G12183-010K InGaAs <abbr title="Photodiode">PD</abbr> [<a href="{{< relref "#citation5" >}}">5</a>].  The gas cell may vary depending on the optical set-up. We will test the set-up in free space for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentrations.

The operation of the architecture is best explained by starting from the microcontroller output (upper left) part of the diagram that controls the <abbr title="Digital-to-Analogue Converter">DAC</abbr>. The <abbr title="Digital-to-Analogue Converter">DAC</abbr> generates a voltage signal consisting of a bias component, line scanning ramp (or triangle) and high frequency sine modulation. These are voltage signals, while lasers are usually current controlled. The voltage controlled current source ($V \rightarrow I  \text{conv.}$) converts this voltage signal into a current signal that drives the laser. 

The emitted light is sent through the gas cell and some of this light is incident on a <abbr title="Photodiode">PD</abbr>. A current signal is generated depending on the amount incident light on the active area of the <abbr title="Photodiode">PD</abbr>. This (typically small) current is converted into a voltage by the <abbr title="Transimpedance Amplifier">TIA</abbr>. The first and second order harmonics are the signals of interest, not the slowly varying ramp (or triangle) signal nor the bias level. The varying ramp (or triangle) and bias signal are filtered out using a high-pass filter. The resulting signal can if necessary be amplified using a <abbr title="Variable Gain Amplifier">VGA</abbr> that is controlled by the microcontroller. Before converting the analogue signal into a digital signal, low-pass filtering is performed. This is done to remove spurious components due to interference and to prevent aliasing. 

The <abbr title="Analogue-to-Digital Converter">ADC</abbr> converts the analogue signal into a digital signal at a fixed sampling rate. This signal is then passed to the <abbr title="Lock-In Amplifier">LIA</abbr> (more details on <abbr title="Lock-In Amplifier">LIA</abbr> at the end of this section). These extract the desired information (the <abbr title="Second Order Harmonic">2f</abbr>) and reference signal (the <abbr title="First Order Harmonic">1f</abbr>) that are combined into a ratio ($R\_{2/1}$). A model is then fitted to this ratio to extract the concentration using a computer.

The <abbr title="Lock-In Amplifier">LIA</abbr> will be implemented on the Explorer 16 Development Board with 100-pin <abbr title="Plug-In Module">PIM</abbr>, a prototyping board from Microchip featuring the dsPIC33FJ256GP710A microcontroller. This microcontroller operates at 40MIPS (<abbr title="Millions of Instructions Per Second">MIPS</abbr>) and features optimised <abbr title="Discrete Signal Processing">DSP</abbr> routines and instructions [<a href="{{< relref "#citation6" >}}">6</a>]. Additionally, it also includes a 12bit <abbr title="Analogue-to-Digital Converter">ADC</abbr>. An external one was selected, since these can provide more accurate analogue-to-digital conversion. The actual concentration extraction is done on a computer, because of the limited computational power of the microcontroller.

The architecture from figure <a href="{{< ref "#3" >}}">3</a> provides a good trade-off between complexity and flexibility. The optical setup can easily be varied for different lasers and gasses. Emitted laser powers can vary drastically (e.g.  <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> vs. <abbr title="Distributed Feedback (Laser)">DFB</abbr> lasers). Using the <abbr title="Variable Gain Amplifier">VGA</abbr>, these differences need not result in a very low voltage signal at the <abbr title="Analogue-to-Digital Converter">ADC</abbr>. The filtering (especially the high-pass filter after the <abbr title="Transimpedance Amplifier">TIA</abbr>), allows to bias the laser for different absorption peaks without needing to change the detector, since the <abbr title="Direct Current">DC</abbr> bias value and triangle signal are filtered out. The input signals can also be controlled easily (from the microcontroller), making an investigation into the number of bits needed for accurate measurement possible. 

In this architecture, we make use of <abbr title="Lock-In Amplifier">LIA</abbr>. These are phase-sensitive detectors that can extract signals near the noise floor. They extract the (slowly varying) amplitude of a sinusoidal signal. Their operation can be explained as follows: the incoming signal $v(t)=A(t)\sin\br{2\pi ft}$ is phase-locked with respect to a reference signal $r(t)=B\sin\br{2\pi ft + \phi}$, these are multiplied and low-pass filtered to obtain the desired amplitude $A(t)$:

<div>
$$
\begin{align}
v(t)r(t) = &\dfrac{A(t)B}{2}\br{\cos{\phi}-\cos\br{4\pi f t + \phi}}\ & \xrightarrow{\text{low pass filter}} \dfrac{A(t)B}{2}\cos{\phi} \propto A(t)
\end{align}
$$
</div>


This ratio still depends on the phase difference between the measured signal $v(t)$ and reference signal $r(t)$. This is resolved by using both an in-phase and quadrature reference signal. 

<abbr title="Lock-In Amplifier">LIA</abbr> are usually implemented in the digital domain because of increased accuracy and dynamic reserve (ratio of the largest tolerable noise signal to the full-scale signal of interest outside the signal band) [<a href="{{< relref "#citation7" >}}">7</a>]. The need for phase locking between the measured signal and the reference signal is the main reason the system uses an <abbr title="Digital-to-Analogue Converter">DAC</abbr> instead of an analogue generation circuit. 

<h2>Drive Signals</h2>

The laser input current is the sum of three signals: a <abbr title="Direct Current">DC</abbr> bias signal to set the operating point, a slow bias modulation that scans over the absorption line and a fast sine modulation that is used to generate the <abbr title="Second Order Harmonic">2f</abbr> harmonic to obtain the gas concentration. 

The frequencies and amplitudes of these signals have been sized to optimally extract the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. The bias modulation must be much slower than the sine modulation, since the bandwidth of the sine modulation is determined by this bias modulation frequency. To remove most of the noise by means of the <abbr title="Lock-In Amplifier">LIA</abbr>, a small bandwidth is required. We will design the system for a 2004nm <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser from Eblana Photonics [<a href="{{< relref "#citation8" >}}">8</a>], measuring around the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption peak at 2003.5nm. A summary of the drive signals is provided in table <a href="{{< ref "#1" >}}">1</a>. 

The triangle signal amplitude was chosen to have an optimal wavelength difference when scanning over the absorption feature while not overdriving the laser. The sine amplitude was chosen such that the resulting wavelength sine modulation has an amplitude of 2.2 times the <abbr title="Half-Width-Half-Maximum">HWHM</abbr> of the absorption peak at 2003.5nm.

The drive signals will be generated using two 6bit R-2R <abbr title="Digital-to-Analogue Converter">DAC</abbr> that are buffered using the LTC1152 amplifier from Linear Technology. The digital <abbr title="Input/Output">IO</abbr>-pins from the microcontroller will be used to drive these. The voltage signals are then added using an analogue adder and finally converted into a current using a linear current source. A simulation of the gain transfer function from the most and least significant bits of the bias modulation <abbr title="Digital-to-Analogue Converter">DAC</abbr> is depicted in figure <a href="{{< ref "#6" >}}">6</a>. 

<table id="table-1">
<caption>Table 1: Signal frequencies and amplitudes that drive the laser.</caption>
<tr>
<th>|C{1cm}C{1.3cm}C{1.3cm}}</th>
<th><abbr title="Direct Current">DC</abbr> bias</th>
<th>bias modulation</th>
<th>sine modulation</th>
</tr>
<tr>
<td>amplitude [mA]</td>
<td>70</td>
<td>19</td>
<td>21.08</td>
</tr>
<tr>
<td>frequency [Hz]</td>
<td>0</td>
<td>10</td>
<td>10000</td>
</tr>
</table>




<figure id="figure-6">
<img alt="ext_abstr:full_transfer_function" src="/images/full_transfer_function.svg">
<figcaption>
Figure 6: Simulated gain from digital most and least significant bits (<abbr title="Most Significant Bit">MSB</abbr> and <abbr title="Least Significant Bit">LSB</abbr> resp.) of the triangle wave to the analogue output of the laser driver.
</figcaption>
</figure>



As expected, the gain in figure <a href="{{< ref "#6" >}}">6</a> is fairly low: -6dB from digital input to output of the <abbr title="Digital-to-Analogue Converter">DAC</abbr> buffer amplifier, an additional -16.5dB at the adder output, and finally -28.1dBS (dB Siemens) for the voltage to current conversion, resulting in -50.6dBS for the most significant bit. The bandwidth of this (simulated) transfer function is 185kHz.





<h2>Detector</h2>

The output current signal from the <abbr title="Photodiode">PD</abbr> needs to be amplified using a <abbr title="Transimpedance Amplifier">TIA</abbr>, the transimpedance was set at 68kΩ and provides a good trade-off between stability (the phase margin is approximately 69.6°), noise performance, bandwidth and gain when using the LT6230 amplifier from Linear Technology.

The high-pass filter has a (measured) cut-off frequency of 1kHz. This sufficiently attenuates the bias modulation and removes the <abbr title="Direct Current">DC</abbr> voltage signal coming out of the <abbr title="Transimpedance Amplifier">TIA</abbr> while not attenuating the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. 

The low-pass filter has a cut-off frequency of 100kHz (measured). This is insufficient to prevent aliasing from occurring at lower frequencies since the sampling frequency is 80kHz. It can still be used to suppress high-frequency signals. A lower cut-off frequency resulted in too much gain imbalance between the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. Both filters employ the Sallen-Key active filter topology using the OPA277 amplifier. 

The system can provide additional amplification by means of a <abbr title="Variable Gain Amplifier">VGA</abbr>. The circuit features the LT1222 amplifier in a non-inverting op-amp circuit configuration. The gain is changed by closing and opening a set of switches that is present in the feedback path. Four gain levels are available: 0dB, 10dB, 20dB and 30dB. 

The signal at the output of the low-pass filter is a single-end signal. The selected AD7687 <abbr title="Analogue-to-Digital Converter">ADC</abbr> from Analogue Devices is a differential <abbr title="Analogue-to-Digital Converter">ADC</abbr>, so we must convert this single-end signal into a differential signal. This is done by means of the THS4130 differential amplifier in a unity-gain configuration. 



<figure id="figure-5">
<img alt="ext_abstr:full_tf_detector" src="/images/full_tf_detector.svg">
<figcaption>
Figure 5: Simulated Bode magnitude plot of the entire transfer function from the <abbr title="Photodiode">PD</abbr> output current to the <abbr title="Analogue-to-Digital Converter">ADC</abbr> input after the single-ended to differential converter.
</figcaption>
</figure>



The overall simulated transfer function is depicted in figure <a href="{{< ref "#5" >}}">5</a>. From this transfer function, we can conclude that the detector has a gain of 96.56dBΩ and 96.33dBΩ for the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components respectively. These values are very close to the ideal value of 68kΩ or 96.65dBΩ. Additionally, the gain offset between both components is very small (less than 0.5dB). 

<h2>Measurements</h2>

We have performed a measurements in high and low <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration environments. The high-concentration was obtained by means of tea lights that were placed in a box with the optics. The low concentration measurement was performed at ambient <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> levels. A low-concentration measurement around the 2002.5nm <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption line is depicted in figure <a href="{{< ref "#7" >}}">7</a>. The set-up consists of an optical path length of 488.5cm where the light passed through ambient <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentrations. The exact concentration is unknown, since there was no reference detector available during testing. 



<figure id="figure-7">
<img alt="ext_abstr:original_update_weak_R" src="/images/original_update_weak_R.svg">
<figcaption>
Figure 7: Measurement in ambient <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> levels and averaged over 10 traces, resulting in an overall measurement time of 1s.
</figcaption>
</figure>



The plot in figure <a href="{{< ref "#7" >}}">7</a> is the result of 10 subsequent measurements of 100ms which are averaged to suppress noise in the signal bands. We can clearly observe the <abbr title="Second Order Harmonic">2f</abbr> main lobe and one side lobe. The other side lobe is not visible though. This indicates that the driver on the one hand does not drive the laser into its correct region or that the laser has non-linear behaviour. This needs to be investigated further in future work. Additionally, we also observe that the height of the two main lobes is not equal. A possible cause of this can be that the <abbr title="Thermoelectric Cooling">TEC</abbr> slightly follows the slow triangle bias modulation. This causes the wavelength to shift due to slight changes in laser temperature. We also observe an additional lobe near 50ms, probably also due to <abbr title="Thermoelectric Cooling">TEC</abbr> variations.



Since the main lobe and one side lobe are visible per scan over the absorption peak in figure <a href="{{< ref "#7" >}}">7</a>, we can conclude that the system is able to extract ambient <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentrations. 



<h2>Conclusion</h2>

We have designed a cost-effective <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> system using discrete off the shelf components that is able to sense ambient <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentrations in a measurement time of 1s. This set-up is flexible and can be easily adopted to other absorption lines and gas cells. The proposed architecture can be further optimised by integrating a <abbr title="Thermoelectric Cooling">TEC</abbr> and applying real-time background subtraction to improve the sensitivity of the system. 

<h2>References</h2>


<ul>
<li id="citation1">[1]: J. Reid and D. Labrie, "Second-Harmonic Detection with Tunable Diode Lasers - Comparison of Experiment and Theory", <em>Applied Physics</em>, 1981.</li>
<li id="citation2">[2]: G. B. Rieker, "Wavelength-modulation Spectroscopy for Measurements of Gas Temperature and Concentration in Harsh Environments", <em>Stanford University</em>, 2006.</li>
<li id="citation3">[3]: "Online Database", http://hitran.iao.ru/home.</li>
<li id="citation4">[4]: S. H. Salati and A. Khorsandi, "Apodized 2f/1f wavelength modulation spectroscopy method for calibration-free trace detection of carbon monoxide in the near-infrared region: theory and experiment", <em>Applied Physics B</em>, 2014.</li>
<li id="citation5">[5]: "InGaAs PIN photodiodes (G12183)", Datasheet, <em>Hamamatsu</em>, Available: <a href='https://www.hamamatsu.com/resources/pdf/ssd/g12183_series_kird1119e.pdf'>https://www.hamamatsu.com/resources/pdf/ssd/g12183_series_kird1119e.pdf</a>.</li>
<li id="citation6">[6]: "dsPIC33FJXXXGPX06A/X08A/X10A", Datasheet, <em>Microchip</em>, 2012.</li>
<li id="citation7">[7]: "About Lock-In Amplifiers", <em>3</em>.</li>
<li id="citation8">[8]: "DFB Laser 2004nm", Datasheet, <em>Eblana Photonics</em>, Available: <a href='http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf'>http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf</a>.</li>
</ul>
