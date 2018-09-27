---
title: Digital Control and Processing
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 5
---






In this chapter, we will establish a theoretical framework from which we can obtain accurate simulation results. We can compare these simulations with the measurement data to obtain the concentration. We will start by laying out how we will tackle concentration extraction using first and second order harmonics. Next, we will design the digital low-pass filters and <abbr title="Lock-In Amplifier">LIA</abbr> implementation. Finally, we will discuss the actual concentration extraction method. 

<h2 id="section-5.3">WMS Framework to Extract Concentration</h2>

Prior to starting to construct the simualtion framework, we will make some assumptions on the input data and background signals that can be present. Background signals can be subdivided into three categories [<a href="{{< relref "#citation1" >}}">1</a>]:

* background signals from etalon effects along the optical path;
* background signals from non-linear modulation effects of the laser;
* background absorption by other gasses present in the gas cell.


The etalon effects (i.e. interference effects due to reflections in the system) cannot be corrected for in the model and should be resolved by means of good optical design. The non-linear laser effects can be incorporated by employing a non-linear laser model that can be fitted to actual measurement data from this laser. An alternative method can also be used where actual measurement data is interpolated to estimate the laser characteristics. This technique is not explorer further in this framework. The presence of other gasses is also not taken into account in the model presented here, but it can be through a simple extension. Measurement data should be corrected for this error prior to comparison with the model. This can be done by measuring the absorption in a region where no features from any of the gasses present in the gas cell arise. The presence of such a region is the largest assumption made during the construction of the subsequent framework. 

One of the common issues with concentration extraction by means of <abbr title="Tuneable Diode Laser Absorption Spectroscopy">TDLAS</abbr> is the need to obtain the power emitted by the laser. The power can be obtained though several sources: an integrated <abbr title="Photodiode">PD</abbr> in the same package as the laser or measurement and calibration using lab equipment. However, amplitude variation may occur and correcting this using the calibration data from a previous trace is inconvenient and inaccurate. Furthermore, such corrections are not possible in the field when we use a lab measured reference. Lasers' output power can also vary over time and partial beam blocking can occur.

These problems can be solved by using not one but two signals at the detector side. By using a fraction of two signals, the dependence on emitted laser power can be eliminated. No calibration is thus required. However, other additional measures are still needed, namely background subtraction to compensate for system imperfection. Every physical system has (limited) non-linear behaviour. This non-linear behaviour will also cause additional contributions to the signals of interest: the first and second order harmonic of the fast sine wave. If we assume that these remain approximately the same in time, then we can model subtract them using a calibration procedure.

The normalisation and background subtraction can be summarised in this formula:

<div>
$$

R\_{2/1} = \sqrt{ \br{\br{\dfrac{I\_{2f}}{R\_{1f}}}\_\text{meas}-\br{\dfrac{I\_{2f}}{R\_{1f}}}\_\text{bg}}^2 + \br{\br{\dfrac{Q\_{2f}}{R\_{1f}}}\_\text{meas} - \br{\dfrac{Q\_{2f}}{R\_{1f}}}\_\text{bg}}^2 }
\tag{5.6}

$$
</div>


here, $I\_i$ and $Q\_i$ are the in-phase and quadrature components of $i$-th harmonic the lock-in signals respectively. $R\_{1f}$ is the amplitude of the first order harmonic. The subscript 'meas' denotes <abbr title="Wavelength Modulation Spectroscopy">WMS</abbr> measurement data from the equipement and 'bg' denotes the background (and other interfering) signals present. These can be removed by means of a calibration (either on- of offline). The typical shape of this ratio is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.15" >}}">3.15</a>.

In contrast to other works (e.g. [<a href="{{< relref "#citation1" >}}">1</a>]), where a rigorous mathematical model is constructed to fit to a single point in of the ratio of harmonics, we will use a numerical model where the system is simulated in the computer. The simulation will then be fitted to the measurement data using the Levenberg-Marquardt algorithm [<a href="{{< relref "#citation2" >}}">2</a>]. 

We will perform the concentration extraction in three phases as depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.16" >}}">5.16</a>. First, data acquisition is performed from the microcontroller. Second, this data is averaged to minimise noise. Furthermore, background subtraction is also performed to improve fitting performance. Third, and finally, the measurement data is fitted to a model of the extracted signals.



<figure id="figure-5.16">
<img alt="detector:WMS_framework" src="/images/WMS_framework.svg">
<figcaption>
Figure 5.16: Steps followed in computer to extract concentration.
</figcaption>
</figure>

 

In the remainder of this chapter, we will focus on the individual aspects of the microcontroller code (section <a href="{{< ref "digital_control_processing.md#section-5.1" >}}">5.1</a> and section <a href="{{< ref "digital_control_processing.md#section-5.4" >}}">5.4</a>) and computer code (section <a href="{{< ref "digital_control_processing.md#section-5.2" >}}">5.2</a>) that make concentration extraction possible according to figure <a href="{{< ref "digital_control_processing.md#figure-5.16" >}}">5.16</a>. We will start with the microcontroller code, followed by the computer code. 

<h2 id="section-5.1">Lock-in Amplifier Design and Implementation</h2>

<abbr title="Lock-In Amplifier">LIA</abbr> are useful to detect very small <abbr title="Alternating Current">AC</abbr> signals, even below the noise floor. This is possible due to phase-locking between the measured signal and a reference signal. This phase-locking in combination with narrowband low pass filtering allows to extract the signal [<a href="{{< relref "#citation3" >}}">3</a>].

First, we will look at general <abbr title="Lock-In Amplifier">LIA</abbr> properties and compare analogue to digital implementations. Subsequently, we will discuss a typical <abbr title="Lock-In Amplifier">LIA</abbr> implementation and finally translate this architecture to the system at hand. <abbr title="Lock-In Amplifier">LIA</abbr> bandwidths are also derived from simulation data.

<h3 id="subsection-5.2">LIA Concept and Properties</h3>

Assume we want to detect a 1kHz signal $v(t)$ with an amplitude of 10nV that is amplified with a factor of 1000 (60dB) by an amplifier with an input noise density of 5nvolt/Hz<sup>1/2</sup>. 
Then we have the following signals at the output if we assume that the bandwidth is 10kHz: $v'(t)$ with an amplitude of 10μV and 0.5mV of broadband noise. Detecting the signal becomes nearly impossible, unless we are able to relate the signal to some kind of a reference signal. This is exactly what a <abbr title="Lock-In Amplifier">LIA</abbr> does. A phase sensitive detector and low pass filter are used to extract the signal, even when it is well below the noise before applying the <abbr title="Lock-In Amplifier">LIA</abbr> [<a href="{{< relref "#citation4" >}}">4</a>].

The phase-sensitive detector is implemented as follows: we have our signal of interest $v(t)=A(t)\sin\br{2\pi f t}$ and a reference signal $r(t)=B\sin\br{2\pi f t + \phi}$ which is phase locked to this signal. These are then multiplied and low-pass filtered:

<div>
$$

v(t)r(t) = \dfrac{A(t)B}{2}\br{\cos{\phi}-\cos\br{4\pi f t + \phi}} \xrightarrow{\text{low pass filter}} \dfrac{A(t)B}{2}\cos{\phi} \propto A(t)

$$
</div>
 

The low pass filter employed above can have a very narrow bandwidth, as to just extract the desired amplitude, which is time-varying, and remove all noise outside the bandwidth around $A(t)$. The above phase-detector output still depends on the phase difference $\sin{\phi}$. This can be alleviated by using an in-phase and quadrature reference signal and combining both output amplitudes. This technique is common in all narrowband transceiver systems (where we speak of so-called $I$/$Q$-components). 

<abbr title="Lock-In Amplifier">LIA</abbr> are usually employed using digital electronics. The full scale signal is first sampled using an <abbr title="Analogue-to-Digital Converter">ADC</abbr> and then processed digitally to extract the desired signal. This approach has many advantages over analogue <abbr title="Lock-In Amplifier">LIA</abbr>. The main problems with analogue <abbr title="Lock-In Amplifier">LIA</abbr> systems are harmonic rejection, output offsets, limited dynamic reserve, and gain error [<a href="{{< relref "#citation5" >}}">5</a>, <a href="{{< relref "#citation4" >}}">4</a>].

All of the above errors are not present in digital implementations. After digital conversion, some quantisation noise is added (usually small compared to the signal) and the remainder of the computations is done without loss of precision, even in fixed point systems losses due to rounding errors are usually small. 

Furthermore, dynamic reserve is limited in analogue systems (up to 60dB), while it can be much higher in digital implementations (for example 100dB). Dynamic reserve is an important characteristic of a <abbr title="Lock-In Amplifier">LIA</abbr>. Dynamic reserve is usually defined as the ratio of the largest tolerable noise signal to the full-scale signal of interest. For example, using the same amplitudes as in the previous example (10μV signal), and a dynamic reserve of 60dB, we can tolerate a noise voltage up to 10mV [<a href="{{< relref "#citation4" >}}">4</a>].

However, the frequency at which this noise appears is important, noise at the frequency of the signal of interest or nearby will not be removed due to filtering. The actual dynamic reserve level consequently can have a shape like the one in figure <a href="{{< ref "digital_control_processing.md#figure-5.12" >}}">5.12</a> [<a href="{{< relref "#citation4" >}}">4</a>]. Noise that appears near or on the signal frequency $f\_\text{ref}$ will not be removed and results in an offset or incorrect gain. Measurement accuracy will thus be limited by this noise. 



<figure id="figure-5.12">
<img alt="DCP:dynamic_reserve" src="/images/dynamic_reserve.svg">
<figcaption>
Figure 5.12: Typical frequency dynamic reserve of a digital <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<h3>Typical Digital LIA Architecture</h3>

A typical <abbr title="Lock-In Amplifier">LIA</abbr> architecture is depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.3" >}}">5.3</a>. We can readily recognise part of the architecture we discussed in figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a>. Similarly, the signal generation is done using a <abbr title="Digital-to-Analogue Converter">DAC</abbr>, since this is the most effective way to create a phase-locked input signal for the analogue measurement system. The resulting signal is sampled using an <abbr title="Analogue-to-Digital Converter">ADC</abbr> on phase-locked timestamps. The <abbr title="Digital-to-Analogue Converter">DAC</abbr> and <abbr title="Analogue-to-Digital Converter">ADC</abbr> can be internal (microcontroller <abbr title="Input/Output">IO</abbr>-pins) or external components. External components are usually interfaced with the microcontroller using protocols such as <abbr title="Serial Peripheral Interface">SPI</abbr> and <abbr title="Inter-Integrated Circuit">I2C</abbr> [<a href="{{< relref "#citation5" >}}">5</a>].



<figure id="figure-5.3">
<img alt="DCP:Digital_LIA_Concept" src="/images/Digital_LIA_Concept.svg">
<figcaption>
Figure 5.3: Conceptual <abbr title="Lock-In Amplifier">LIA</abbr> implementation using digital microcontroller.
</figcaption>
</figure>



The mixers (multipliers) of the <abbr title="Lock-In Amplifier">LIA</abbr> use <abbr title="Lookup Table">LUT</abbr>. A <abbr title="Lookup Table">LUT</abbr> is a table of precomputed values that are fixed in program memory. Precomputing these $I$/$Q$ values is much more time-efficient than computing the correct values for every sample. These <abbr title="Lookup Table">LUT</abbr> are also quite small. This will be discussed in further detail in subsection <a href="{{< ref "digital_control_processing.md#subsection-5.3" >}}">5.3</a>.

Since both the <abbr title="Digital-to-Analogue Converter">DAC</abbr> output and sine <abbr title="Lookup Table">LUT</abbr> are controlled from a single reference signal, these are also phase-locked when performing the multiplication to obtain the in-phase and quadrature components. By means of in-phase and quadrature signal processing, the output can be independent of the phase shift in the measurement system (subsection <a href="{{< ref "digital_control_processing.md#subsection-5.2" >}}">5.2</a>). This also means both in-phase and quadrature values must be extractable from the <abbr title="Lookup Table">LUT</abbr>. 
By mixing (multiplying) the reference signal with the sampled frequency, the signal of interest gets shifted to <abbr title="Direct Current">DC</abbr> and double the frequency. Afterwards, filtering is applied to remove the high frequency components. A single low-pass filter is typically inadequate to implement the narrowband filter at the mixer output since these filters required too many coefficients [<a href="{{< relref "#citation5" >}}">5</a>]. Typical filtering bandwidths vary depending on the application, but bandwidths of 1Hz are not uncommon. 

To simplify filtering for this constrained system (both in time, since new data arrives at a fixed rate; and in space, since we need to store the filter coefficients and fill delay buffers), filtering is split up into several stages where the signal is decimated to a lower sampling rate. For instance, a 20kHz signal with a 10Hz amplitude modulation can be sampled at 80kHz and subsequently filtered and decimated to obtain the same signals at 8kHz. This signal is then filtered and decimated again to 1kHz and again to 200Hz. The system thus applies subsequent decimation factors of 10, 8 and 5 respectively on the input signal after the previous decimation and filtering stage. A filter with a bandwidth of 10Hz can easily be realised at this final sample frequency of 200Hz with a limited number of coefficients. 

<h3 id="subsection-5.1">LIA Bandwidths for First and Second Order Harmonic Extraction</h3>

This theoretical discussion is based on prior works [<a href="{{< relref "#citation6" >}}">6</a>, <a href="{{< relref "#citation7" >}}">7</a>, <a href="{{< relref "#citation8" >}}">8</a>]. We will provide an overview of important results from literature to size and design the bandwidth of the <abbr title="Lock-In Amplifier">LIA</abbr>. 

The light intensity falling on the <abbr title="Photodiode">PD</abbr> is given by the law of Beer-Lambert:

<div>
$$

I(\lambda) = I\_0(\lambda) \exp\br{-\alpha(\lambda)L} 
\tag{5.4}

$$
</div>


In equation <a href="{{< ref "digital_control_processing.md#equation-5.4" >}}">5.4</a> $I\_0(\lambda)$ is the wavelength dependent output power of the laser, $\alpha$ is the absorption coefficient and $L$ is the optical path length. Here, we have used the wavelength ($\lambda$) as variable and not the wavenumber ($\nu=1/\lambda$) as in equation <a href="{{< ref "background.md#equation-1.1" >}}">1.1</a>. 

For now, we will not make any assumptions on the absorption profile yet. We will assume that the absorption feature is scanned using a single ramp from $\lambda\_\text{min}$ to $\lambda\_\text{max}$ in a linear fashion during a time interval of size $T$. This results in the following time-wavelength relationship:

<div>
$$

\lambda(t) = \lambda\_\text{c} + \dfrac{\lambda\_\text{max} - \lambda\_\text{min}}{T} t,\qquad t\in \sbr{-\dfrac{T}{2}, \dfrac{T}{2}},\quad \lambda\_\text{c} = \dfrac{\lambda\_\text{max}+\lambda\_\text{min}}{2} \tag{5.5}

$$
</div>



For small modulation depths of the sine wave, it can be proven that the first and second order components at the receiver are proportional to the first and second order derivative of the absorption profile [<a href="{{< relref "#citation8" >}}">8</a>] respectively. Using a proportionality factor $B\_i$, we can now write the first ($x\_1$) and second order harmonic signal ($x\_2$) as:

<div>
$$
\begin{align}
x\_1(\lambda) &= B\_1\dod{\alpha}{\lambda}(\lambda) \tag{5.2} \\
x\_2(\lambda) &= B\_2\dod[2]{\alpha}{\lambda}(\lambda) \tag{5.3}
\end{align}
$$
</div>


We can now compute the Fourier transform $\mathcal{F}$ of these signals (assuming they are only defined during a period $T$) using equation <a href="{{< ref "digital_control_processing.md#equation-5.5" >}}">5.5</a>, equation <a href="{{< ref "digital_control_processing.md#equation-5.2" >}}">5.2</a> and equation <a href="{{< ref "digital_control_processing.md#equation-5.3" >}}">5.3</a>:

<div>
$$
\begin{align}
\mathcal{F}\sbr{x\_1}(f) &= B\_1'\int\_{\sfrac{-T}{2}}^{\sfrac{T}{2}} \dod{\alpha}{\lambda}(\lambda(t)) \exp\br{-j2\pi ft} \dif t \tag{5.1}\\
\mathcal{F}\sbr{x\_2}(f) &= B\_2'\int\_{\sfrac{-T}{2}}^{\sfrac{T}{2}} \dod[2]{\alpha}{\lambda}(\lambda(t)) \exp\br{-j2\pi ft} \dif t
\end{align}
$$
</div>


The above integral can be evaluated numerically. In case of ambient sensing, pressure broadening dominates and we can assume the Lorentzian line shape (equation <a href="{{< ref "background.md#equation-1.4" >}}">1.4</a>) [<a href="{{< relref "#citation1" >}}">1</a>]. For the selected <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption line at 2003.5nm (see table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a>) and a time interval $T$ of 50ms, we obtain the spectra from figure <a href="{{< ref "digital_control_processing.md#figure-5.2" >}}">5.2</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.1" >}}">5.1</a> for the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> which scans over a wavelength range of 530pm. These spectra have been normalised such that the area under the curve is one. Other lasers have similar spectra, but can have a different wavelength range. We will continue the discussion for the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>, since the reasoning for the other lasers is similar.

These spectra depict frequencies relative to the centre frequency of <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. These are broadened because their amplitude varies over time. We can interpret this as an amplitude modulation of the carrier frequency:

<div>
$$

x\_i(t) \propto a\_i(t)\sin\br{2\pi f\_{\text{sine}, i} t}, \qquad i=1,2

$$
</div>


Where $a\_i(t)$ is proportional to the first and second derivatives of the absorption profile. These derivatives along with the absorption line are depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.15" >}}">5.15</a>. Zeros at the centre frequencies (0Hz in figure <a href="{{< ref "digital_control_processing.md#figure-5.1" >}}">5.1</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.2" >}}">5.2</a>) occur because the integral of the time domain plots is zero (right plots in figure <a href="{{< ref "digital_control_processing.md#figure-5.15" >}}">5.15</a>). There is an equal amount of area under the curve that is positive and negative. The small amplitude variations that can be observed in the tails of figure <a href="{{< ref "digital_control_processing.md#figure-5.1" >}}">5.1</a> are due to limiting the integration in equation <a href="{{< ref "digital_control_processing.md#equation-5.1" >}}">5.1</a> to finite frequency and wavelength intervals.



<figure id="figure-5.1">
<img alt="DCP:1f_spectrum" src="/images/1f_spectrum.svg">
<figcaption>
Figure 5.1: Normalised Fourier transform of first order sine harmonic due to wavelength scan over selected <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption line at 2003.5nm ($\mathcal{F}[x\_1](f)$).
</figcaption>
</figure>



<figure id="figure-5.2">
<img alt="DCP:2f_spectrum" src="/images/2f_spectrum.svg">
<figcaption>
Figure 5.2: Normalised Fourier transform of second order sine harmonic due to wavelength scan over selected <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption line at 2003.5nm ($\mathcal{F}[x\_2](f)$).
</figcaption>
</figure>





<figure id="figure-5.15">
<img alt="DCP:time_absorption_1f_2f" src="/images/time_absorption_1f_2f.svg">
<figcaption>
Figure 5.15: Time domain plots of a simulated scan over the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> absorption peak at 2003.5nm (left), first order sine harmonic $x\_1(t)$ (upper right) and second order sine harmonic $x\_2(t)$ (lower right).
</figcaption>
</figure>



From figure <a href="{{< ref "digital_control_processing.md#figure-5.15" >}}">5.15</a> we can also observe that it is not needed to scan over the maximum wavelength ranges we put up front in table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a>, especially for the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser. Since most of the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> variation is within an interval of 0.5nm of the absorption line (the laser scans over a wavelength interval of 0.53nm from 0 to $T$ in figure <a href="{{< ref "digital_control_processing.md#figure-5.15" >}}">5.15</a>). A larger wavelength variation means that the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> variation due to the absorption peak will happen faster, resulting in a larger bandwidth and less resolution due to increased noise. As a result, it makes sense to limit the wavelength variation.

The extension from this ramp signal to the triangle wavelength modulation signal can easily be made as follows. First, the signal needs to be mirrored and translated in the time domain to obtain the triangle wave. The spectrum is only shifted and rotated by means of this transformation, while retaining the same shape. Next, we need to periodically extend the time domain function. This can be done by using a convolution of this triangle function and a Dirac comb (impulse train) $III\_T$ with a spacing of $T=0.1s$ between Dirac delta functions. The time-domain signal will then have a triangle shape and a frequency of 10Hz. The resulting spectrum will only contain components at discrete frequencies spaced 10Hz apart.

A more mathematical description is detailed below for the <abbr title="First Order Harmonic">1f</abbr> component. This can readily be translated for the <abbr title="Second Order Harmonic">2f</abbr> component by replacing $x\_1$ with $x\_2$:
	
<div>
$$
\begin{align}
\text{base transform:} && x\_1(t) \xleftrightarrow{\mathcal{F}}& \mathcal{F}[x\_1](f) = \mathcal{X}\_1(f) \\
\text{triangle extension:} && x\_1(t) + x\_1(T-t) \xleftrightarrow{\mathcal{F}}& \mathcal{X}\_1(f) + \exp\br{-j2\pi fT}\mathcal{X}\_1(-f) \\
\text{$x\\_1(t)$ is a real signal:} && x\_1(t) + x\_1(T-t) \xleftrightarrow{\mathcal{F}}& (1+\exp\br{-j2\pi fT})\mathcal{X}\_1(f) \\
\text{periodic extension:} && \sbr{x\_1(t) + x\_1(T-t)}*III\_T(t) \xleftrightarrow{\mathcal{F}}& \dfrac{1}{T}(1+\exp\br{-j2\pi fT})\mathcal{X}\_1(f) III\_{1/T}(t)
	  \end{align}
$$
</div>


<h3 id="subsection-5.3">LIA Operation and Filter Design</h3>

In this section, we will design a set of digital filters to decimate and extract the frequencies of interest. Since we designed the system to operate using a triangle wave of 10Hz, the ramp will scan over the absorption line at 20Hz, resulting in a period of 50ms and the Fourier transforms from figure <a href="{{< ref "digital_control_processing.md#figure-5.1" >}}">5.1</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.2" >}}">5.2</a>. We will also discuss the microcontroller <abbr title="Lock-In Amplifier">LIA</abbr> mixing routines in this subsection. 

<h4>LIA Mixing Operation</h4>

A <abbr title="Lock-In Amplifier">LIA</abbr> shifts the frequency of interest to <abbr title="Direct Current">DC</abbr> using a mixing operation followed by a low-pass filter. This mixing operation is done in the microcontroller and phase locked to the input. We will apply quadrature sampling, thus at four times the rate of the highest signal frequency. This results in a sample frequency of 80kHz. 

Quadrature sampling is the easiest type of sampling, since a sample is either passed, inverted or set to zero, since the multiplication coefficient is either 1, -1 or 0. The set of constellation points is depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.10" >}}">5.10</a>. This results in a <abbr title="Lookup Table">LUT</abbr> of four values for the <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>.

For the <abbr title="First Order Harmonic">1f</abbr> signal, we cannot apply this technique directly. One possibility is to first low-pass filter the signal, decimate it by a factor of 2 and then perform the mixing operation. However, it is more efficient to use a more complex constellation with eight points. This constellation is depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.9" >}}">5.9</a>. This results in a <abbr title="Lookup Table">LUT</abbr> of eight values for the <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>.

Two routines have been written in assembly to perform these mixing operations very efficiently using the <abbr title="Discrete Signal Processing">DSP</abbr> instructions on the microcontroller. The code itself can be found in chapter <a href="{{< ref "microcontroller_code.md#chapter-A2.1" >}}">A2.1</a>. The routine for the <abbr title="Second Order Harmonic">2f</abbr> component is simple, since there is no need to perform fixed point multiplications. However, this is required for the <abbr title="First Order Harmonic">1f</abbr> component at the four blue points in the constellation from figure <a href="{{< ref "digital_control_processing.md#figure-5.9" >}}">5.9</a>.



<figure id="figure-5.10">
<img alt="DCP:constellation_2f" src="/images/constellation_2f.svg">
<figcaption>
Figure 5.10: Constellation of <abbr title="Lock-In Amplifier">LIA</abbr> mixing points for <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<figure id="figure-5.9">
<img alt="DCP:constellation_1f" src="/images/constellation_1f.svg">
<figcaption>
Figure 5.9: Constellation of <abbr title="Lock-In Amplifier">LIA</abbr> mixing points for <abbr title="First Order Harmonic">1f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<h4>Filtering After LIA Multiplication</h4>

Since the frequency bands have infinate tails, we will set the filter bandwidth such that 95% of the signal is passed by the filter. This is a trade-off between filter width and noise rejection. Using this rule, we obtain bandwidths of 392Hz and 518Hz for the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components respectively for the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>. The bandwidth definition used here, is illustrated in figure <a href="{{< ref "digital_control_processing.md#figure-5.8" >}}">5.8</a>. Table <a href="{{< ref "digital_control_processing.md#table-5.1" >}}">5.1</a> provides a summary of the bandwidths for all three lasers when using the maximum wavelength difference from table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a>. 



<figure id="figure-5.8">
<img alt="DCP:bandwidth_illustration" src="/images/bandwidth_illustration.svg">
<figcaption>
Figure 5.8: Illustration of our bandwidth definition for the <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<table id="table-5.1">
<caption>Table 5.1: <abbr title="Lock-In Amplifier">LIA</abbr> bandwidth such that 95% of the energy is captured.</caption>
<tr>
<th>laser</th>
<th><abbr title="First Order Harmonic">1f</abbr> [Hz]</th>
<th><abbr title="Second Order Harmonic">2f</abbr> [Hz]</th>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr></td>
<td>392</td>
<td>518</td>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>214</td>
<td>122</td>
</tr>
<tr>
<td><abbr title="Carbon Monoxide">CO</abbr>  <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>1360</td>
</table>


A graphical depiction of the transfer function of all four filters for the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> is made in figure <a href="{{< ref "digital_control_processing.md#figure-5.5" >}}">5.5</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.6" >}}">5.6</a>. Both the ideal floating point (H1-4) and fixed point versions (DSP H1-4) are depicted. Quantisation has limited effects on the filters. The largest effect is visible in the fourth and final filter for both components. This is due to the filter length (more than 200 coefficients), resulting in larger quantisation errors because many small filter coefficients are present.

These filters are digital equiripple filters with a passband ripple of 0.04dB for the first filter and 0.02dB for the other three filters. These filters should have sufficient attenuation (at least 60dB) to attenuate noise and other components (such as the <abbr title="First Order Harmonic">1f</abbr> signal in the <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>). The filters were designed to have 70dB of attenuation to make sure we have at least 60dB attenuation for the fixed point filters.



<figure id="figure-5.5">
<img alt="DCP:LIA_1f_filters_TF" src="/images/LIA_1f_filters_TF.svg">
<figcaption>
Figure 5.5: Transfer functions of digital low-pass filters for first order harmonic <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<figure id="figure-5.6">
<img alt="DCP:LIA_2f_filters_TF" src="/images/LIA_2f_filters_TF.svg">
<figcaption>
Figure 5.6: Transfer functions of digital low-pass filters for second order harmonic <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



The decimation factor and filter relationships are summarised in figure <a href="{{< ref "digital_control_processing.md#figure-5.11" >}}">5.11</a> for both the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components. A slightly different scheme is needed for the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser since the bandwidth of this laser is much larger. However, if the wavelength variation is limited, as discussed in a previous paragraph, a similar scheme can be applied. The bandwidths of the final filters can be different due to the different characteristics of the <abbr title="Carbon Monoxide">CO</abbr> absorption line. 

The first two filters in figure <a href="{{< ref "digital_control_processing.md#figure-5.5" >}}">5.5</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.6" >}}">5.6</a> have the same filtering coefficients for both signals. Do note that we do not decimate immediately after the <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr> to prevent aliasing from occurring even though half of the samples are zero due to the quadrature sampling. 



<figure id="figure-5.11">
<img alt="DCP:decimation_filtering_scheme" src="/images/decimation_filtering_scheme.svg">
<figcaption>
Figure 5.11: Decimation and filtering scheme for <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr>.
</figcaption>
</figure>



<h2 id="section-5.4">Signal Generation and LIA Interrupt Routines</h2>

The final part of the microcontroller code is putting everything together to form the signal processing and generation unit. Several measures have been taken to ensure that the code runs in a deterministic order that is able to provide a phase-lock between the input and output of the system. 

<h3 id="subsection-5.4">Signal Generation and Sampling Interrupt Routine</h3>

The input signals are generated using from digital samples using <abbr title="Digital-to-Analogue Converter">DAC</abbr>. Since we created our own <abbr title="Digital-to-Analogue Converter">DAC</abbr> in section <a href="{{< ref "laser_driver.md#section-3.1" >}}">3.1</a>, we need to set the digital output lines ourselves. We will update them at a fixed rate of 320kHz (or $\sfrac{1}{125}$th of the instruction clock frequency). This means that the digitally generated sine-wave has 32 updates every period. This should be sufficient to create an accurate sine wave. The triangle wave can pass through 64 levels during its positive and negative ramp if we use all bits of the triangle <abbr title="Digital-to-Analogue Converter">DAC</abbr>. It will thus require an update rate of 1280Hz. 320kHz is a multiple of 1280Hz, thus both can be integrated into the same interrupt routine.

320kHz is also a multiple of the <abbr title="Analogue-to-Digital Converter">ADC</abbr> sampling rate of 80kHz. Consequently, this can also be included in the same interrupt routine. Since we do not sample upon every interrupt, we use a branching behaviour in the routine to only execute what is needed. It is important that all of these are included in the same interrupt routine for phase-locking purposes. Otherwise, the handing of one interrupt routine might stall another one and this will lead to time offsets and make accurate phase-locking impossible.

Figure <a href="{{< ref "digital_control_processing.md#figure-5.14" >}}">5.14</a> is a schematic representation of the signal generation interrupt routine. The three green blocks indicate code that has been written in such a way as to make it execute in a fixed number of cycles independent of control signals. In figure <a href="{{< ref "digital_control_processing.md#figure-5.14" >}}">5.14</a>, ''update'' means that a value is written to the output <abbr title="Digital-to-Analogue Converter">DAC</abbr> pins. This value can be the same (often the case for the triangle generation) or an updated value (always the case for the sine wave). This write operation for the triangle is need to obtain a phase-lock with the <abbr title="Analogue-to-Digital Converter">ADC</abbr> conversion signal.

After conversion by the <abbr title="Analogue-to-Digital Converter">ADC</abbr>, the <abbr title="Direct Memory Access">DMA</abbr> reads the value over <abbr title="Serial Peripheral Interface">SPI</abbr>. The <abbr title="Direct Memory Access">DMA</abbr> is a special module that allows to transfer data with peripherals such as the <abbr title="Serial Peripheral Interface">SPI</abbr> and <abbr title="Inter-Integrated Circuit">I2C</abbr> module without the need for <abbr title="Central Processing Unit">CPU</abbr> intervention.

After performing all the <abbr title="Input/Output">IO</abbr>, the values are updated for the next interrupt cycle and the interrupt is terminated. Since the interrupt is triggered at a rate of 320kHz, it needs to terminate as fast as possible to allow other interrupt routines to be handled without missing an interrupt. 



<figure id="figure-5.14">
<img alt="DCP:signal_generation_interrupt" src="/images/signal_generation_interrupt.svg">
<figcaption>
Figure 5.14: Signal generation interrupt routine.
</figcaption>
</figure>



<figure id="figure-5.7">
<img alt="DCP:LIA_interrupt" src="/images/LIA_interrupt.svg">
<figcaption>
Figure 5.7: <abbr title="Lock-In Amplifier">LIA</abbr> processing and data transmit interrupt routine.
</figcaption>
</figure>



<h3>LIA Interrupt Routine</h3>

Data from the <abbr title="Analogue-to-Digital Converter">ADC</abbr> comes in at a constant rate of 1.28Mbps and needs to be processed to a signal of only 64kbps (there are four streams of 16kbps: $I\_\text{1f}$, $Q\_\text{1f}$, $I\_\text{2f}$, and $Q\_\text{2f}$). This can be done in the main code, but in order not to lose time waiting for the data to be processed, this has also been written into an interrupt routine. 

The <abbr title="Direct Memory Access">DMA</abbr> fills a sample buffer using the <abbr title="Serial Peripheral Interface">SPI</abbr> read triggers from subsection <a href="{{< ref "digital_control_processing.md#subsection-5.4" >}}">5.4</a>. The <abbr title="Direct Memory Access">DMA</abbr> operates in so-called ping-pong mode, where one buffer is filled by the <abbr title="Direct Memory Access">DMA</abbr> and another is being processed. When the <abbr title="Direct Memory Access">DMA</abbr> buffer is full, an interrupt is generated and the interrupt routine from figure <a href="{{< ref "digital_control_processing.md#figure-5.7" >}}">5.7</a> handles the incoming data.

The interrupt that handles the sampled data and processes it by passing it through the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr> is depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.7" >}}">5.7</a>. First, the output values of the <abbr title="Second Order Harmonic">2f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr> are computed. Then, the <abbr title="First Order Harmonic">1f</abbr> <abbr title="Lock-In Amplifier">LIA</abbr> values are computed using the same data. Finally all data is aggregated into a new buffer and transported over <abbr title="Universal Asynchronous Receiver/Transmitter">UART</abbr> to a computer for further processing. 

The transfer to the computer is also handled by the <abbr title="Direct Memory Access">DMA</abbr>. If the <abbr title="Direct Memory Access">DMA</abbr> is not busy transferring <abbr title="Serial Peripheral Interface">SPI</abbr> data from the <abbr title="Analogue-to-Digital Converter">ADC</abbr> (this transfer has priority), it will transmit the data to the computer. The receiving computer programme should have a dedicated <abbr title="Universal Asynchronous Receiver/Transmitter">UART</abbr> read thread to handle the incoming data without data loss due to buffer overflow. 


<h2 id="section-5.2">Fitting Measurement and Simulation: Concentration Extraction</h2>

After the signal has passed from the laser driver, through the detector and the <abbr title="Discrete Signal Processing">DSP</abbr>, we can finally extract the concentration. This will be done offline using the simple procedure depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.13" >}}">5.13</a>. 



<figure id="figure-5.13">
<img alt="DCP:fit_model_conc" src="/images/fit_model_conc.svg">
<figcaption>
Figure 5.13: Concentration extraction using the Levenberg-Marquardt algorithm from the measured data.
</figcaption>
</figure>



The script first captures raw <abbr title="Universal Asynchronous Receiver/Transmitter">UART</abbr> data from the microcontroller and stores this in a buffer. Once the buffer is full, the data can be averaged and fitted. The fitting algorithm not only needs to estimate the concentration, but also a set of meta-parameters that cannot be fixed. These paraters are the wavelength scanning range and a measurement sample offset (we will come back to this).

We include the wavelength range as one the fit parameters since the triangle amplitude may vary slightly over time, but more importantly, since we do not exactly know the wavelength range. As a consequence, we will need to determine this also during concentration extraction.

The other meta-parameter is an offset. This offset arises from the fact that the measurement is performed using a triangle wave with a frequency of 10Hz and 64 levels. As a result, we switch between levels at a rate of 1280Hz. The output signal however, has an output sample rate of 1kHz. Consequently, we will have 100 samples per triangle ramp. It is also clear that we cannot have a one-to-one relationship between the triangle level and the output sample due to the different frequencies (1280Hz and 1000Hz). The output samples are thus numbered to indicate where a period starts and where it ends. However, these numbers have a shift with respect to the triangle period. This offset is non-deterministic (it can vary after a rest of the system). The microcontroller does not take care of synchronising this offset, this should be done on the computer. Once the offset has been determined, it remains the same for the remaining measurements (until reboot) provided that the estimate was correct. The output signal further arranges subsequent samples into a groups of samples for more efficient transportation (the overhead due to the synchronisation is reduced this way). 

The concentration extraction itself is based on an accurate simulation of the entire opto-electrical circuit from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a>. This simulation creates the same $R\_{2/1}$ ratio that we can extract from the measurement data. By fitting the simulated $R\_{2/1}$ ratio to the measured one, we can extract the concentration, since this is an input parameter of the simulation.

A simplified simulation version of all of the components from figure <a href="{{< ref "system.md#figure-2.2" >}}">2.2</a> has been written in Python. These are all linked together to form a \lstinline|FitComponent| object. This component's parameters will then be fitted to the <abbr title="Universal Asynchronous Receiver/Transmitter">UART</abbr> data that is loaded from file.

<h3>Real Time Incoming Signal Inspection Using a GUI</h3>

It is currently not possible to estimate the concentration in real time, however is is possible to do a visual inspection of the incoming data in real time. For this, we have written a <abbr title="Graphical User Interface">GUI</abbr> in Python. A screenshot of the interface is depicted in figure <a href="{{< ref "digital_control_processing.md#figure-5.4" >}}">5.4</a>. The main purpose of this <abbr title="Graphical User Interface">GUI</abbr> is the validation of concentration data before extracting the data. This is thus mainly intended for debugging purposes of the set-up and to check correct <abbr title="Thermoelectric Cooling">TEC</abbr> setting.

This <abbr title="Graphical User Interface">GUI</abbr> depicts both the <abbr title="First Order Harmonic">1f</abbr> (top plane) and <abbr title="Second Order Harmonic">2f</abbr> (bottom plane) components. The user can also change the averaging interval and offset such that the a measurement period starts at position 0. Additionally, the user can also _calibrate_ the system. This means that the current measurement data will be saved and subtracted from future data. Measurements can be performed relative to a certain reference value. This is similar to the background subtraction we discussed in section <a href="{{< ref "digital_control_processing.md#section-5.3" >}}">5.3</a> (equation <a href="{{< ref "digital_control_processing.md#equation-5.6" >}}">5.6</a>).






<figure id="figure-5.4">
<img alt="DCP:GUI" src="/images/GUI.svg">
<figcaption>
Figure 5.4: <abbr title="Graphical User Interface">GUI</abbr> for visual inspection of the incoming <abbr title="First Order Harmonic">1f</abbr> (top plane) and <abbr title="Second Order Harmonic">2f</abbr> (bottom plane) components.
</figcaption>
</figure>




<h2>Evaluating the Accuracy of the System</h2>

After the <abbr title="Lock-In Amplifier">LIA</abbr> bandwidth sizing, we have almost all of the characteristics required to assess if the system will be able to resolve \pm 10ppm concentration differences and meet the specification we put up front in section <a href="{{< ref "system.md#section-2.1" >}}">2.1</a>. 

However, though we are able to compute the expected noise that falls within the signal bandwidths cased by the detector, we are unable to assess the noise coming from the laser diodes, since we do not have the relative intensity noise specification nor the amount of phase noise in case of the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser [<a href="{{< relref "#citation9" >}}">9</a>, <a href="{{< relref "#citation10" >}}">10</a>]. As a consequence, we did not calculate the amount of noise falling within the signal bands.

<h2>Conclusion</h2>

In this chapter, we discussed the most important aspects of the digital control and how we will link the measurements to simulations performed on the computer. We started by constructing the ratio $R\_{2/1}$ from equation <a href="{{< ref "digital_control_processing.md#equation-5.6" >}}">5.6</a> which will be used to estimate concentration.

This ratio $R\_{2/1}$ relies heavily on the use of <abbr title="Lock-In Amplifier">LIA</abbr>. The structure and operation of the two <abbr title="Lock-In Amplifier">LIA</abbr> we employ in the system is discussed in section <a href="{{< ref "digital_control_processing.md#section-5.1" >}}">5.1</a>. 

One of the most important aspects of proper <abbr title="Lock-In Amplifier">LIA</abbr> operation is a good phase lock between the driving signals and the sampling (and thus also the <abbr title="Discrete Signal Processing">DSP</abbr> operations). How we can achieve this phase lock is discussed in section <a href="{{< ref "digital_control_processing.md#section-5.4" >}}">5.4</a>. In the same section we also discuss how the <abbr title="Lock-In Amplifier">LIA</abbr> operation is implemented using a combination of low-pass filtering and decimation.

In section <a href="{{< ref "digital_control_processing.md#section-5.2" >}}">5.2</a>, we discussed how we will process the incoming <abbr title="Lock-In Amplifier">LIA</abbr> data on a computer and perform the concentration extraction using an offline algorithm. Real-time inspection of the <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> data is possible using a simple <abbr title="Graphical User Interface">GUI</abbr>.

<ul>
<li id="citation1">[1]: G. B. Rieker, "Wavelength-modulation Spectroscopy for Measurements of Gas Temperature and Concentration in Harsh Environments", <em>Stanford University</em>, 2006.</li>
<li id="citation2">[2]: K. Levenberg, "A Method for the Solution of Certain Non-Linear Problems in Least Squares", <em>Quarterly of Applied Mathematics</em>, vol. 2, no. 2, 1944.</li>
<li id="citation3">[3]: G. B. Armen, "Phase sensitive detection: the lock-in amplifier", 2008.</li>
<li id="citation4">[4]: "About Lock-In Amplifiers", <em>3</em>.</li>
<li id="citation5">[5]: D. Wenn, "Implementing Digital Lock-In Amplifiers Using the dsPIC DSC", <em>AN1115</em>, 2007.</li>
<li id="citation6">[6]: K. Duffin, A. J. McGettrick, W. Johnstone, G. Stewart and D. G. Moodie, "Tunable Diode-Laser Spectroscopy With Wavelength Modulation: A Calibration-Free Approach to the Recovery of Absolute Gas Absorption Line Shapes", <em>JOURNAL OF LIGHTWAVE TECHNOLOGY</em>, vol. 25, no. 10, 2007.</li>
<li id="citation7">[7]: C. S. Goldenstein, C. L. Strand, I. A. Schultz, K. Sun, J. B. Jeffries and R. K. Hanson, "Fitting of calibration-free scanned-wavelength- modulation spectroscopy spectra for determination of gas properties and absorption lineshapes", <em>Applied Optics</em>, vol. 53, no. 3, 2014.</li>
<li id="citation8">[8]: R. Engelbrecht, "A compact NIR fiber-optic diode laser spectrometer for CO and CO<sub>2</sub>: analysis of observed 2f wavelength modulation spectroscopy line shapes", <em>Spectrochimica Acta Part A Molecular and Biomolecular Spectroscopy</em>, vol. 60, no. 14, 2005.</li>
<li id="citation9">[9]: "DFB Laser 2004nm", Datasheet, <em>Eblana Photonics</em>, Available: <a href='http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf'>http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf</a>.</li>
<li id="citation10">[10]: R. Wang, S. Sprengel, A. Malik, A. Vasiliev, G. Boehm, R. Baets, M. Amann and G. Roelkens, "Heterogeneously integrated III--V-on-silicon 2.3x µm distributed feedback lasers based on a type-II active region", <em>Applied Physics Letters</em>, 2016.</li>
</ul>
