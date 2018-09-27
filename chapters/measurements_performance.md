---
title: Measurements and Performance
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 6
---

In this chapter, a summary of the measurements is provided. First, we will start with measurements performed on each of the circuits to verify that the simulations correspond well with the physical realisation. After verification of these electrical characteristics, the adoptions that were made to the system to improve performance are discussed followed by concentration measurements. 

# Circuit Performance Measurements

In this section, the gain performance of different circuits is compared to the simulations for most circuits. For some of the circuits, it is not possible to easily obtain the gain transfer function. Instead, we have included the DAC response in these cases. This methodology is applied to each of the seven circuits we designed in chapter <a href="{{< ref "laser_driver.md#chapter-3.1" >}}">3.1</a> and chapter <a href="{{< ref "detector.md#chapter-4.1" >}}">4.1</a>.

These performance measurements are performed on the first prototype, which consists of several boards: a laser driver board that includes the DAC and voltage controlled current source; a receiver board that includes the TIA, high-pass filter and VGA; a second receiver board that houses the low-pass filter, single-end to differential converter and ADC and finally two small boards for the PD and laser. Images of these board are included in figure <a href="{{< ref "measurements_performance.md#figure-6.16" >}}">6.16</a>. The different hardware blocks are annotated on the figure. The schematics and board layouts can be found in chapter <a href="{{< ref "PCB.md#chapter-A1.1" >}}">A1.1</a>. All of the measurements described in this chapter were performed on this prototype.

A board that fits all of the above smaller board onto one board (the second prototype) is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.17" >}}">6.17</a>. This board is a second version layout. This board has the advantage that it can be directly plugged into the microcontroller development board, resulting in a drastic decrease of wires in the set-up. The layout is also provided in chapter <a href="{{< ref "PCB.md#chapter-A1.1" >}}">A1.1</a>.

![First PCB prototype consisting of three boards: one laser driver board and two detector boards.](/images/PCB1.svg){#fig:measurements:PCB1}

![Second PCB prototype consisting of all the previous' prototype circuits and a laser connector (not mounted in this picture).](/images/PCB2.svg){#fig:measurements:PCB2}

## Digital-to-Analogue Converter

The signal source of our system is the DAC, so accurate signal generation is important. Figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a> and Figure <a href="{{< ref "measurements_performance.md#figure-6.11" >}}">6.11</a> depict the output signals of the two DAC. Both signals are physical signals and thus imperfect compared to their simulated variants. The most important features are annotated on these \namecrefs{measurements:10Hz_triangle_buffer}.

### Digital-to-Analogue Converter for Triangle Signal

The triangle wave signal has a period of 100ms. The period is annotated for visual inspection on the figure. This period corresponds to the desired frequency of 10Hz.

We can observe several imperfections of the DAC circuit. Sometimes we observe that there is no transition. This is due to resistor variation. The resistors use have an error of 1%, resulting in an error of about \pm 1LSB. In the annotated transition, the signal transitions from an output of \lstinline|011111| to \lstinline|100000|. The MSB (the left bit) has the highest error, and in this realisation there is no transition observable due to these errors. 

Another effect of imperfect resistor values is reduced amplitude variations. During the transition from \lstinline|001111| to \lstinline|010000|, we observe a step size that is too small. Again, this is due to deviating resistor values. 

The overall voltage swing between minimum and maximum value is not 3.3V as assumed during simulation, but approximately 3V. This is however well between the tolerances of the voltages of the digital <abbr title="Input/Output">IO-pins [<a href="{{< relref "#citation1" >}}">1</a>]. The resulting current swing will thus be 10% smaller, resulting in a smaller wavelength swing. However, we will still be able to scan over the absorption line.

A final point we highlight is the delayed transition. This was done intentionally to make the signal easier to generate. The triangle signal has 64 transitions, if we would immediately start decreasing (increasing) the amplitude after reaching the maximum (minimum), we would have 126 transitions per period. The triangle switching frequency (i.e. the frequency at which the triangle voltage is updated) then not be a multiple of the sine switching frequency (i.e. the frequency at which the sine voltage is updated). We delay the start of the next triangle ramp (up and down) with one transition to alleviate this problem.

Effects such as finite slew rate and bandwidth are hardly visible in figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a> because the frequency is too low compared to the bandwidth. These effects are however, visible during the sine wave generation.

![Signal at the output of the triangle buffer: 10Hz triangle voltage signal. Different imperfections in the signal are annotated, including the lack of transitions and limited transition amplitude due to resistor value deviations and delayed transition to simplify signal generation.](/images/10Hz_triangle_buffer.svg){#fig:measurements:10Hz_triangle_buffer}

### Digital-to-Analogue Converter for Sine Signal

A signal trace measured with the oscilloscope (and averaged over 8 traces) of the output of the sine wave DAC is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.11" >}}">6.11</a>. Again, we observe that the signal is not perfect. However, due to the increased frequency, we observe other effects. 

First, we verify the signal frequency, the period is 100μs, resulting in a frequency of 10kHz. This is de desired value. 

The first imperfection we observe is annotated with number 1. The voltage _decreases_ here. This is due to the finite bandwidth of the signal. The higher order harmonics are attenuated, and more importantly, experience a phase shift. It is this phase shift of higher order harmonics that results in the amplitude decrease. 

We also observe that the signal the signal is not symmetric. When the signal has a negative slope, we observe a different signal decay than during the positive slope. This is clearly visible during the change in slope, indicated by number 2 on the figure. 

The signal amplitude does not reach its expected values of 0V and 3.3V. The high voltage is within specifications. However, what is more noticeable is the non-zero low value of 0.10V. This is not due to the amplifier, since it has a very low offset voltage [<a href="{{< relref "#citation2" >}}">2</a>]. To investigate this further, a voltage trace was captured of the <abbr title="Input/Output">IO-pin associated with the MSB of the sine wave DAC. This is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.7" >}}">6.7</a>. 

From figure <a href="{{< ref "measurements_performance.md#figure-6.7" >}}">6.7</a>, we learn that the output voltage of the digital <abbr title="Input/Output">IO-pin associated with the MSB is not zero. We can observe two effects: capacitive discharge and the switching behaviour of the other <abbr title="Input/Output">IO-pins in the increasing and decreasing voltage levels. The minimum voltage of this pin is 0.08V. A similar effect is observed at the other digital <abbr title="Input/Output">IO-pins. The R-2R ladder circuit will add these, resulting in the 0.1V offset we observe in figure <a href="{{< ref "measurements_performance.md#figure-6.11" >}}">6.11</a>

Other effects such as limited transition amplitude (i.e. the effect annotated with ''no transition'' in figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a>) due to resistive tolerances, as we observed in figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a>, are also present in this figure. But the transitions for a sine wave are less regular, making these harder to observe.

![Signal at the output of the sine buffer: 10kHz sine voltage signal. Point 1 indicates a short decrease in amplitude due to the limited bandwidth and resulting phase changes of harmonics. Point 2 illustrates asymmetry, resulting in even order higher order harmonics.](/images/10kHz_sine_buffer.svg){#fig:measurements:10kHz_sine_buffer}

Using the same oscilloscope, we also obtained a Fourier transform of the sine signal. This is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.12" >}}">6.12</a>. The spectrum is normalised such that the actual frequency (10kHz) as a a magnitude of 0dBV. Since the signal is not symmetric at the half periods (e.g. the point denoted by 2 in figure <a href="{{< ref "measurements_performance.md#figure-6.11" >}}">6.11</a>), the even order harmonics will not be zero. Indeed, we observe that the second order harmonic has an amplitude of -51dBV. This will also appear at the detector and interfere with the 2f component due to the absorption peak.

![Averaged scope signal of the MSB of the sine DAC at the digital output of the microcontroller.](/images/offset_sine_DAC.svg){#fig:measurement:offset_sine_DAC}

![Normalised spectrum of the fast sine signal (10kHz) at the output of its DAC buffer. Here, even order harmonics are present due to imperfections in the signal generation.](/images/10kHz_sine_buffer_spectrum.svg){#fig:measurements:10kHz_sine_buffer_spectrum}

### Analogue Adder Circuit

The transfer function of the analogue adder circuit from figure <a href="{{< ref "laser_driver.md#figure-3.6" >}}">3.6</a> was not measured. This circuit cannot easily be driven using an external voltage (due to the board layout) and the same amplifier as for the DAC ladders is used with the same bandwidth compensation. However, to have some idea of its performance, we have measured this response for the 1f and 2f component frequencies. These two points have been annotated in figure <a href="{{< ref "measurements_performance.md#figure-6.22" >}}">6.22</a>.

From the two measured points of the gain transfer function of the analogue in figure <a href="{{< ref "measurements_performance.md#figure-6.22" >}}">6.22</a>, we can conclude that this part of the circuit indeed performs as expected for the two frequencies of interest. The roll-off was not verified.

![Simulated and measured gain transfer function of the analogue adder circuit for the fast sine modulation.](/images/analogue_adder.svg){#fig:measurements:analogue_adder}

## Voltage Controlled Current Source

The gain of the voltage controlled current source is important, since we want the 10kHz sine signal to be passed through without attenuation. Figure <a href="{{< ref "measurements_performance.md#figure-6.23" >}}">6.23</a> confirms that the circuit behaves as expected. We observe that the cut-off frequency has slightly shifted to lower frequencies, but this does not form an issue since the there is no noticeable attenuation at the 1f frequency. 

The low frequency gain of slightly less than -28dBS is also correct. This circuit was tested by using a 15Ω resistor instead of the CO~2~ DFB laser. The same resistance value was used in the simulations, since there was no simulation model available of any of the lasers.

![Simulated and measured gain transfer function of the voltage controlled current source.](/images/current_source.svg){#fig:measurements:current_source}

## Transimpedance Amplifier

We did not measure the full transfer function of the TIA since this requires exciting the laser with a certain sinusoidal current signal, which is not trivial. Driving the system by means of the designed laser driver circuit is possible, but the frequency range is limited. Since we are mainly interested in the gain for the 1f and 2f components, we only measured at these frequencies. 

The used measurement method is the following: first, we apply a DC current signal to the laser. The PD response (current) is then measured once using a current metre and also once using the TIA output voltage. From these two measurements, we can compute the reference transimpedance value. Next, we feed the laser with the input current signals generated by our own DAC for the 1f and 2f frequencies and measure the amplitude of the output voltage.

The PD output current measured for constant input current at the laser is 27.25μA and the output TEC value is 1.60V. This results in a transimpedance value of 58.7kΩ, while the soldered resistor has a resistance of 50.8kΩ. This deviation is larger than expected and is possibly due to the measurement method. The TIA output voltage was not measured at the same time as the PD response current. Furthermore, the LT6230 op-amp can have an input offset current of up to 10μA [<a href="{{< relref "#citation3" >}}">3</a>]. If we take this uncertainty into account, we can conclude that the transimpedance falls within the expected range.

When applying the same input signal amplitude at 10kHz and 20kHz, the measured TIA output voltages are 408.0mV and 403.8mV respectively. Both values are reasonably close to each other (within 1%) and there was also already a small amount of roll-off for the transimpedance gain transfer functions in figure <a href="{{< ref "detector.md#figure-4.9" >}}">4.9</a>. We can thus conclude that the TIA operates as expected for the frequencies of interest. 

The observant reader will have noticed that the transimpedance value does not match the ones calculated in table <a href="{{< ref "detector.md#table-4.2" >}}">4.2</a>. This was done deliberately to be absolutely sure that we do not overload any of the components in this first prototype. We have also a collimator at our disposal to increase the amount of incident power on the PD to further compensate this smaller gain.

## High-pass Filter

One of the more important cut-off frequencies is the cut-off frequency of the high-pass filter. We want to have sufficient attenuation for the low frequency components, but not alter the 1f and 2f signals. Figure <a href="{{< ref "measurements_performance.md#figure-6.14" >}}">6.14</a> depicts both the simulated and measured voltage gain transfer function. 

From figure <a href="{{< ref "measurements_performance.md#figure-6.14" >}}">6.14</a> we can see that the simulation and measurements correspond well an that the 10Hz triangle signal is attenuated while the 1f and 2f signals are not altered.

![Simulated and measured voltage gain transfer function of the high-pass filter.](/images/HP_filter.svg){#fig:measurements:HP_filter}

## Variable Gain Amplifier

The simulations and measurements of the VGA for its four different gain configuration are depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.18" >}}">6.18</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.19" >}}">6.19</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.20" >}}">6.20</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.21" >}}">6.21</a>. The measurements correspond to the simulations, but not as well as for the high-pass filter and voltage controlled current source.

As expected, we observe a resonance phenomenon in figure <a href="{{< ref "measurements_performance.md#figure-6.18" >}}">6.18</a>, however, it is slightly more pronounced in the measurements with the actual circuit. This underestimation of the resonance phenomenon is also visible in figure <a href="{{< ref "measurements_performance.md#figure-6.19" >}}">6.19</a>. Here, we do not expect a resonance phenomenon, but nevertheless, it is present. A possible explanation for this can be that the switch model is not accurate enough and has additional parasitics, resulting in a shifted zero or pole. 

In case the gain settings are higher, there is a better match between measurement and simulation, as is observed in figure <a href="{{< ref "measurements_performance.md#figure-6.20" >}}">6.20</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.21" >}}">6.21</a>. The cut-off frequency is slightly higher, but this is not a real issue, since we do not expect this stage to provide much filtering. 

Additionally, the gain mismatch between the 1f and 2f frequencies in all plots is limited for all configurations and should not pose problems. In figure <a href="{{< ref "measurements_performance.md#figure-6.19" >}}">6.19</a>, the gain mismatch is slightly higher than the expected value, probably due to additional parasitics cause by the switching circuit. Since the signal passes through this switching element, this might attenuate the signal slightly, resulting in the small gain that can be observed in figure <a href="{{< ref "measurements_performance.md#figure-6.18" >}}">6.18</a>. 

![Simulated and measured voltage gain transfer function of the VGA for a gain of 0dB.](/images/VGA_gain1.svg){#fig:measurements:VGA_gain1}

![Simulated and measured voltage gain transfer function of the VGA for a gain of 10dB.](/images/VGA_gain2.svg){#fig:measurements:VGA_gain2}

![Simulated and measured voltage gain transfer function of the VGA for a gain of 20dB.](/images/VGA_gain3.svg){#fig:measurements:VGA_gain3}

![Simulated and measured voltage gain transfer function of the VGA for a gain of 30dB.](/images/VGA_gain4.svg){#fig:measurements:VGA_gain4}

## Low-pass Filter

Of similar importance as the high-pass filter, is the low-pass filter. Attenuation is desired for high frequency signals beyond 20kHz. We observe excellent correspondence between the simulation and the realisation figure <a href="{{< ref "measurements_performance.md#figure-6.15" >}}">6.15</a>.

![Simulated and measured voltage gain transfer function of the low-pass filter.](/images/LP_filter.svg){#fig:measurements:LP_filter}

## Analogue-to-Digital Converter Driver

Similar to the filter measurements, VGA and current source measurement, we have also measured the gain of the single-end to differential converter (ADC driver). This measurement is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.13" >}}">6.13</a>. This gain transfer function was measured as follows: the signal is applied at the input of the low-pass stage. The resulting voltage signals at the input and outputs of the ADC driver is measured then to obtain the transfer function from figure <a href="{{< ref "measurements_performance.md#figure-6.13" >}}">6.13</a>. We applied this measurement technique to ensure that we took the output impedance of the low-pass filter into account.

In the gain measurement of figure <a href="{{< ref "measurements_performance.md#figure-6.13" >}}">6.13</a>, there is noticeable gain deviation from the simulation. Additionally, we also observe a resonance phenomenon where the gain drops from slightly positive to negative gain (or attenuation). The low-pass behaviour at higher frequencies is as expected.

This gain deviation is an issue, but not a severe one. The gain difference between 10kHz and 20kHz in figure <a href="{{< ref "measurements_performance.md#figure-6.13" >}}">6.13</a> is very small. In a future design, this gain shift that starts at 2kHz should be resolved.

A possible cause of this deviation can be that there is a zero and a pole at these frequencies in the output impedance of the OPA277 amplifier, which is used in the low-pass filter design. The open-loop output impedance is included in the datasheet and indeed shows a zero and pole around these frequencies [<a href="{{< relref "#citation4" >}}">4</a>]. 

![Simulated and measured voltage gain transfer function of the single-end to differential converter (ADC driver).](/images/ADC_driver.svg){#fig:measurements:ADC_driver}

![Sine signal at the output of DAC and ADC conversion signals, captured with an oscilloscope and averaged over 8 traces. This indicates phase lock between both signals. Digital interference due to SPI data transfer can be observed as well.](/images/sine_ADC_phase_lock.svg){#fig:measurement:sine_ADC_phase_lock}

## Analogue-to-Digital Converter Phase Lock

One of the fundamental aspects of the correct operation of the system is phase locking. Both triangle signals and sine signals need to be phase locked (this is not visualised in a figure, since the signal frequency difference is too small, making it difficult to visualise). Additionally, the ADC sampling also needs to be phase locked to these.

Figure <a href="{{< ref "measurements_performance.md#figure-6.8" >}}">6.8</a> depicts one period of the sine signal at the output of the DAC sine generation buffer and the ADC conversion signal. A rising edge on the ADC triggers a conversion of the signal amplitude and thus samples the signal at that time. The oscilloscope was locked on the conversion signal and 8 traces are averaged. 

If the signals were not phase locked, the sine signal would have a weird shape or reduced amplitude due to the averaging of the oscilloscope. However, this is not the case, indicating both signals are indeed phase locked.

In this figure, we can also observe digital interference (due to averaging, the influence is very limited). Most of the interference is due to the SPI data transfers from the ADC to the microcontroller, one of these transfers is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.8" >}}">6.8</a>. The system is designed such that the SPI data transfer occurs in between sampling intervals and immediately after the maximum conversion time. This way, we can minimise digital interference in the sampled signal.

# Full System First and Second Order Harmonic Measurements# 

For the gas concentration measurements, we will use the set-up depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.33" >}}">6.33</a>. The path length was increased from 3.5m to 4.885m to have more pronounced 1f and 2f components. The set-up can be covered with a box to decrease scattering due to air movement (an important source of noise) and increase the concentration by means of putting several tea lights in the box. A reference detector was not available, we will use visual inspection instead to assess the system performance since the concentration extraction algorithm does not yet perform as expected.

![Measurement set-up. The light travels over a path length of 488.5cm before falling on the PD.](/images/measurement_setup.svg){#fig:measurements:measurement_setup}

A CO~2~ concentration measurement in a closed environment with increased CO~2~ levels is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.30" >}}">6.30</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.31" >}}">6.31</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.32" >}}">6.32</a>. The TEC is tuned to a thermistor resistance of 19.25kΩ, resulting in an output wavelength of approximately 2002.5nm. The measurement was not performed over the proposed peak from table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a> for two reasons: this measurement is performed with the CO~2~ DFB laser which has a higher output power for this wavelength than at 2003.5nm and the measurement environment is dry. There is thus no risk of water absorption interference.  Since we desire a single accurate measurement to be achieved within 1s, we have averaged over 10 subsequent measurements.

From these \namecrefs{measurements:initial_R}, we can conclude that accurate concentration extraction is impossible from these measurements. We do not recognise the $R\_{2/1}$ shape we expect from the simulations (see for example figure <a href="{{< ref "laser_driver.md#figure-3.15" >}}">3.15</a>), since the 2f LIA output (depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.32" >}}">6.32</a>) is too noisy. The system generates too many (time dependent) harmonics at the receiver, which end up masking the weak 2f signal.

We are able to resolve the 1f signal, since this signal is much stronger, but there is also a lot of noise present in the measurement. Additionally, we observe that the measurement is not stable since there is a large difference in amplitude between both annotated dips in time. We also observe that the these dips are not equally strong, especially in the blue curve. 

![Ratio $R\_{2/1}$ (equation <a href="{{< ref "digital_control_processing.md#equation-5.6" >}}">5.6</a>) after 10 averages from measurement with TEC setting of 19.25kΩ (the resulting laser temperature should be around 8C) in an environment with increased CO~2~ concentration. (The exact value is unknown).](/images/initial_R.svg){#fig:measurements:initial_R}

![Normalised 1f LIA output after 10 averages from measurement with TEC setting of 19.25kΩ (the resulting laser temperature should be around 8C) in an environment with increased CO~2~ concentration. (The exact value is unknown).](/images/initial_R_1f.svg){#fig:measurements:initial_R_1f}

![Normalised 2f LIA output after 10 averages from measurement with TEC setting of 19.25kΩ (the resulting laser temperature should be around 8C) in an environment with increased CO~2~ concentration. (The exact value is unknown).](/images/initial_R_2f.svg){#fig:measurements:initial_R_2f}

We can conclude that the laser driver generates too many harmonics (probably due to the triangle generation) that fall within the 1f and 2f LIA bands. We also observe that there is a difference between the peak strengths of the 1f component, this can be due to TEC settling behaviour or due to the harmonics in the system.

We will now slightly redesign the system to fix these issues. To this end, we will increase the measurement frequency, such that we can rule out any TEC related issues and decrease the bandwidth of the DAC circuits to attenuate higher order harmonics stronger.

# Redesign to Increase Measurement Sensitivity

During initial measurement we observed that there are too many harmonics in the output signal, as a consequence, we will lower the bandwidth of the slow modulation (triangle) DAC. Furthermore, the TEC, which needs to keep the laser temperature constant, might be too fast and attempts to make it slower had limited success. The TEC might want to settle on the slow frequency variation caused by the triangle wave. To alleviate this possible issue, we will also increase the triangle modulation frequency. 

As a consequence of these problems, the system was partially redesigned. We will first discuss briefly the most important changes to the system and subsequently discuss measurement results. A new TEC was not designed due to time constraints. However, a suggestion for a possible TEC design with respect to this prototype is discussed in chapter <a href="{{< ref "improvements.md#chapter-7.1" >}}">7.1</a>.

## Overview of Redesign

A bandwidth reduction of the laser driver's DAC is unavoidable since a major issue is the harmonics generated by these. As a result, the triangle generation might no longer be perfect; a triangle wave requires harmonics (see figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a>). As a result, we will also investigate the effects of changing the triangle modulation to a sine modulation. From now on, we will no longer speak of a triangle modulation (previously at 10Hz) to avoid confusion with the sine modulation (at 10kHz). Instead, we will speak of _bias modulation_ if we mean the slow modulation.

The TEC might attempt to lock on the bias modulation. However, we observe no settling deviations when only the sine signal is present, indicating that the TEC has correctly settled. However, we cannot increase this frequency without consequence. As discussed in subsection <a href="{{< ref "digital_control_processing.md#subsection-5.1" >}}">5.1</a>, a higher frequency of the triangle wave will also broaden the spectrum around the 1f and 2f components. This will allow more noise and other harmonics to enter the signal band. Thus, we need to keep the bandwidth of the bias modulation as low as possible.

A good trade-off between TEC settling behaviour and 1f and 2f bandwidth was found for a bias modulation that is a triangle wave with a  frequency of 100Hz or a sine wave with a frequency of 80Hz. We will now discuss the redesign and provide additional analysis for the different aspects of the setup. We will start by redesigning the DAC and reinvestigating the LIA bandwidth requirements. 

### Redesign of Bias Modulation DAC

The bias modulation wave coming out of the DAC wave will also have additional harmonics, but as we discussed in section <a href="{{< ref "laser_driver.md#section-3.1" >}}">3.1</a>, we can reduce the bandwidth of the DAC to provide some filtering by adding a larger compensation capacitor. The harmonics at the 1f and 2f frequencies are strongly attenuated this way. 

The bias modulation DAC compensation capacitance was set at 22nF and that of the (fast) sine modulation was increased from 47pF to 94pF (a second capacitance was added in parallel). A simulation of the gain from the MSB of the bias modulation DAC is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.3" >}}">6.3</a>. Again, we observe the expected pass-band gain of -6dB, this time with a bandwidth of 1.2kHz. The 1f and 2f components have an attenuation of -24.4dB and -30.4dB due to the limited bandwidth. Decreasing the bandwidth further resulted in a slew rate that was too low. 

![Bode gain plot from the MSB of the redesigned bias modulation DAC with decreased bandwidth.](/images/DAC_slow_sine.svg){#fig:measurement:DAC_slow_sine}

### Analysis of the Impact of Sine Modulation On the LIA Bandwidth

Another important parameter of the system is the bandwidth requirement of the LIA. This will be determined by how rapidly the amplitude of the 1f and 2f components vary over time. Due to the usage of a sine wave, we no longer have a constant variation. Additionally, the variation is highest at the absorption peak's maximum, making good peak resolution more difficult. 

Based on the fact that a sine modulation has a non-constant rate of change compared to a triangle modulation, we can readily conclude that the 1f and 2f components will have a larger bandwidth for the sine wave modulation at 80Hz compared to the triangle modulation at the same frequency. To assess the increase, we perform the similar simulations to those from subsection <a href="{{< ref "digital_control_processing.md#subsection-5.1" >}}">5.1</a>.

We repeat the same simulations as figure <a href="{{< ref "digital_control_processing.md#figure-5.1" >}}">5.1</a>, figure <a href="{{< ref "digital_control_processing.md#figure-5.2" >}}">5.2</a> and figure <a href="{{< ref "digital_control_processing.md#figure-5.15" >}}">5.15</a> in figure <a href="{{< ref "measurements_performance.md#figure-6.1" >}}">6.1</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.2" >}}">6.2</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.9" >}}">6.9</a> but now with a frequency of 80Hz for both a sine wave and a triangle wave and for the CO~2~ DFB laser, since we will not perform measurements with the VCSEL. This way, we can assess both the impact of the increased frequency and change in modulation type. As expected, the bandwidths of a sine modulated signal are indeed higher. This is also confirmed by the sharper peaks in the time domain plot of figure <a href="{{< ref "measurements_performance.md#figure-6.9" >}}">6.9</a>. A summary of the different bandwidths is provided in table <a href="{{< ref "measurements_performance.md#table-6.1" >}}">6.1</a>. The required bandwidths for the case of 100Hz triangle bias modulation are also included. Simulation results are similar to those of figure <a href="{{< ref "measurements_performance.md#figure-6.1" >}}">6.1</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.2" >}}">6.2</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.9" >}}">6.9</a> and are thus not depicted here.

<table id="table-6.1">
<caption>Table 6.1: LIA bandwidths for 80Hz sine and triangle wave modulations; and 100Hz triangle modulation.</caption>
<tr>
<th></th>
<th>1f [Hz]</th>
<th>2f [Hz]</th>
</tr>
<tr>
<td>80Hz  triangle modulation</td>
<td>1416</td>
<td>1877</td>
</tr>
<tr>
<td>80Hz  sine modulation</td>
<td>2218</td>
<td>2944</td>
</tr>
<tr>
<td>100Hz triangle modulation</td>
<td>1771</td>
<td>2347</td>
</tr>
</table>

![Normalised Fourier transform of first order sine harmonic due to wavelength scan using a 80Hz sine modulated signal over selected CO~2~ absorption line at 2003.5nm ($\mathcal{F}[x\_1](f)$).](/images/1f_spectrum.svg){#fig:measurement:1f_spectrum}

![Normalised Fourier transform of second order sine harmonic due to wavelength scan using a 80Hz sine modulated signal over selected CO~2~ absorption line at 2003.5nm ($\mathcal{F}[x\_2](f)$).](/images/2f_spectrum.svg){#fig:measurement:2f_spectrum}

![ime domain plots of a simulated 80Hz sine scan over the CO~2~ absorption peak at 2003.5nm (left), first order sine harmonic $x\_1(t)$ (upper right) and second order sine harmonic $x\_2(t)$ (lower right).](/images/time_absorption_1f_2f.svg){#fig:measurement:time_absorption_1f_2f}

The bandwidths from table <a href="{{< ref "measurements_performance.md#table-6.1" >}}">6.1</a> are clearly much larger than those from the 10Hz triangle wave in subsection <a href="{{< ref "digital_control_processing.md#subsection-5.1" >}}">5.1</a>. As a result, we need to increase the output sample rate, since an output sample rate of 1kHz no longer suffices. We will send data to the computer with a sample rate of 8kHz. The microcontroller will thus send LIA data at 512kbps, which is getting closer to the maximum rate of a single <abbr title="Universal Asynchronous Receiver/Transmitter">UART connection (1Mbps for most systems). This indicates that we cannot increase the modulation frequencies much further without applying some compression in the microcontroller. Another consequence of this bandwidth increase is increased noise in the captured signal.

The output rate has changed, so the decimation factors of the different filtering stages have also changed. The new decimation and filtering scheme is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.24" >}}">6.24</a> and features only three filters per LIA compared to four in the initial version from figure <a href="{{< ref "digital_control_processing.md#figure-5.11" >}}">5.11</a>.

A new set of digital filters has also been designed and is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.1" >}}">6.1</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.2" >}}">6.2</a>. The final filters (which are the most important ones) have the bandwidths of the 80Hz sine described in table <a href="{{< ref "measurements_performance.md#table-6.1" >}}">6.1</a> and transition from the pass band to the stop band in a 150Hz region. Smaller transition regions result in too much deviation between the ideal and fixed point filters due to the increased length and quantisation errors.

![Decimation and filtering scheme for 1f and 2f LIA in the case of a 80Hz sine wave modulation.](/images/decimation_filtering_scheme_80Hz.svg){#fig:measurements:decimation_filtering_scheme_80Hz}

![Transfer functions of digital low-pass filters for first order harmonic LIA for the case of a 80Hz slow sine wave modulation.](/images/LIA_1f_filters_TF.svg){#fig:measurement:LIA_1f_filters_TF}

![Transfer functions of digital low-pass filters for second order harmonic LIA for the case of a 80Hz slow sine wave modulation.](/images/LIA_2f_filters_TF.svg){#fig:measurement:LIA_2f_filters_TF}

# Measurements with Increased Bias Modulation Frequency# 

Using this redesigned set-up, we perform measurements on the same set-up depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.33" >}}">6.33</a>. Again, we select the same absorption peak as measurement point by setting the TEC to 19.35kΩ, resulting in $\lambda\_\text{c} = 2002.5nm$. Again, there is no means to obtain the exact concentration, but we will use the same technique to increase the concentration as in section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a>.

Two measurements using the 80Hz bias sine modulation are depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.26" >}}">6.26</a>. We immediately observe that the $R\_{2/1}$ ratio has improved. We can identify the strong 2f peak. One of the two side lobes is missing though. In the right scan, we observe a slight increase of the $R\_{2/1}$ ratio (around 11.5ms) this can be due to several reasons: the laser's output wavelength is no longer linear in this region, the detector system distorts the measurement such that this peak is not visible or the driver does not force the laser into its correct operating region are among the possible causes. 

In the same figure, we also observe several non-linear effects that are time independent. These effects are possibly due to the imperfections in the DAC, which were discussed in figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a>.

A final observation we can make in figure <a href="{{< ref "measurements_performance.md#figure-6.26" >}}">6.26</a> is that the traces are again not symmetric. The side-lobes do not even follow the same trend as the main lobes. Again, this can be due to the electronics or due to TEC influence. Since we depict a measurement at time $t\_1$ and a measurement 1.25s later, we can conclude that the system does not suffer from drift (on a small time-scale) and that the imperfections are consistently present.

For comparison, we have also depicted the same measurement, but with a 100Hz bias triangle modulation in figure <a href="{{< ref "measurements_performance.md#figure-6.28" >}}">6.28</a>. Again, we observe the same non-linear spikes. To be absolutely sure that there are no other interferers present, we also depict the $R\_{2/1}$ ratio around a different absorption peak.

The second absorption peak (''other $\lambda\_c$'' in the legend of figure <a href="{{< ref "measurements_performance.md#figure-6.28" >}}">6.28</a>) is located at 2002.99nm and was selected by setting the TEC to 15.65kΩ. We can also observe an additional non-linear spike that was masked in the other measurements. We can observe that the three observed spikes are separated by the same time difference. However, a counter argument against the DAC being the cause is that these spikes doe not seem to occur at the expected time indices. The same observation as in figure <a href="{{< ref "measurements_performance.md#figure-6.28" >}}">6.28</a> can be made here: the absence of the second side lobe, and difference in main lobe strength.

![Redesigned system with a sine bias modulation of 80Hz. Measurement in high CO~2~ concentration environment and averaged over 80 traces, resulting in an overall measurement time of 1s.](/images/fast_sine_strong_R.svg){#fig:measurements:fast_sine_strong_R}

![Redesigned system with a triangle bias modulation of 100Hz. Measurement in high CO~2~ concentration environment and averaged over 100 traces, resulting in an overall measurement time of 1s.](/images/fast_triangle_strong_R.svg){#fig:measurements:fast_triangle_strong_R}

We have also performed measurements in ambient CO~2~ concentrations. These measurements for the sine and triangle modulations depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.27" >}}">6.27</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.29" >}}">6.29</a> respectively.

![Redesigned system with a sine bias modulation of 80Hz. Measurement in ambient at CO~2~ levels and averaged over 80 traces, resulting in an overall measurement time of 1s.](/images/fast_sine_weak_R.svg){#fig:measurements:fast_sine_weak_R}


![Redesigned system with a triangle bias modulation of 100Hz. Measurement in ambient at CO~2~ levels and averaged over 100 traces, resulting in an overall measurement time of 1s.](/images/fast_triangle_weak_R.svg){#fig:measurements:fast_triangle_weak_R}

In figure <a href="{{< ref "measurements_performance.md#figure-6.27" >}}">6.27</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.29" >}}">6.29</a>, we can still observe the main and side lobes, especially in figure <a href="{{< ref "measurements_performance.md#figure-6.29" >}}">6.29</a>. However, this figure indicates that there are still too many imperfections in the measurement to enable \pm 10ppm measurements. Additionally, more non-linear peaks are visible in these measurements. These are much weaker, and as a result barely visible in figure <a href="{{< ref "measurements_performance.md#figure-6.26" >}}">6.26</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.28" >}}">6.28</a>.

# Lowering the Bias Modulation Frequency

The updated design from section <a href="{{< ref "measurements_performance.md#section-6.4" >}}">6.4</a> does not completely function as desired. As a result, the system was partially redesigned a second time to enable more accurate extraction of the 1f and 2f components.

Since the settling behaviour of the TEC remains a possible issue of the previous redesign, we decided to lower the modulation frequency instead of increasing it. Additionally, we no longer consider the bias modulation as a continuous modulation, but more as a sequence of discrete steps that set a different bias current for the laser. This has major implications on the way the data should be fitted on the computer. As a consequence, the bias modulation in this design is again a (very slow) triangle wave, and not longer a sine wave as in the previous section.

In this second redesign, measurements are performed much slower. A single triangle value lasts for 5s, half of this interval is used to allow the TEC to settle around the new biasing point. As a consequence, we are unable to use the temperature to tune the laser wavelength. During this 2.5s interval, the microcontroller does not send data to the computer. During the other 2.5s interval, it sends the 1f and 2f LIA data with a sample frequency of 400Hz.

Again, we can do a similar bandwidth calculation as in subsubsection <a href="{{< ref "measurements_performance.md#subsubsection-6.1" >}}">6.1</a>, but this is not really needed. The bias modulation is now so slow, that the bandwidth will also be very small, smaller than practical to apply digital filtering with this bandwidth. As a result, we have designed the same filters for the 1f and 2f components and fixed the bandwidth at 3Hz and a transition width of 3Hz. This is sufficient to filter out most of the noise and non-linearity of the system.

The new filtering and decimation scheme is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.25" >}}">6.25</a> and features four stages, resulting in a large decimation factor of 200. We did not lower the output frequency further, since this required a DMA buffer size that was too large to be practical and a complete rewrite of the filtering and decimation process was otherwise needed. The set of accompanying digital filters is plotted in figure <a href="{{< ref "measurements_performance.md#figure-6.4" >}}">6.4</a>

![Decimation and filtering scheme for 1f and 2f LIA.](/images/decimation_filtering_scheme_very_slow.svg){#fig:measurements:decimation_filtering_scheme_very_slow}

![Transfer functions of digital low-pass filters for first and second order harmonic LIA.](/images/LIA_1f_filters_2_TF.svg){#fig:measurement:LIA_1f_filters_2_TF}

The incoming data at the computer at 400Hz is averaged per bias point of the triangle. As a result, we have 128 samples for the entire trace. Due to this change in how the data is handled, the fitting system needs to be updated as well to match this change. This is also future work.

# Measurements with Slow Bias Modulation

Similar to section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a> and section <a href="{{< ref "measurements_performance.md#section-6.2" >}}">6.2</a>, we will again perform measurements using the same set-up in high and low CO~2~ concentration environments. To illustrate the cancellation of the actual electro-optical gain in the system, a strong measurement (no beam blocking) and a weak measurement (partial beam blocking) were captured in a high CO~2~ environment. The result is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.35" >}}">6.35</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a>. 

The measurements from figure <a href="{{< ref "measurements_performance.md#figure-6.35" >}}">6.35</a>, figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a> depict the output data from the microcontroller, which is averaged per measurement point and plotted. The system was only swept once over the entire triangle wave. The resulting measurement time is 640s. 

From figure <a href="{{< ref "measurements_performance.md#figure-6.35" >}}">6.35</a>, we can indeed conclude that the $R\_{2/1}$ ratio does not change if the light beam is partially blocked. As expected, the 1f and 2f components are effected equally strong. This can be observed in figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a>. In this measurement, we can also observe that the two absorption peaks have approximately equal strength now. However, the second side-lobe is again not visible in figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a>.

This absence of the second side-lobe can also be predicted from figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a>, since the 2f component is maximal at the inflection points (see also subsection <a href="{{< ref "digital_control_processing.md#subsection-5.1" >}}">5.1</a>). In the measurement from figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a>, we do not observe such a point after obtaining the maximum at approximately 100s or before the maximum at 530s. This needs further investigation whether this is a problem caused by the detector, driver, laser or PD.

In figure <a href="{{< ref "measurements_performance.md#figure-6.36" >}}">6.36</a> and figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a> we can also observe the imperfections of the DAC clearly. Two of these points have been annotated in figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a>. As indicated in figure <a href="{{< ref "measurements_performance.md#figure-6.10" >}}">6.10</a>, the transitions are not always as accurate due to resistor deviations. In a future prototype, these custom DAC might need to be replaced by a commercial variant to increase performance, or resistors with lower tolerances should be used.

![Measured ratio $R\_{2/1}$ after second redesign. Measurement in high CO~2~ concentration environment using stepped bias modulation, resulting in a measurement time of 640s.](/images/slow_strong_R.svg){#fig:measurements:slow_strong_R}

![1f measurement after redesign. Measurement in high CO~2~ concentration environment using stepped bias modulation, resulting in a measurement time of 640s.](/images/slow_strong_R_1f.svg){#fig:measurements:slow_strong_R_1f}

![2f measurement after second redesign. Measurement in high CO~2~ concentration environment using stepped bias modulation, resulting in a measurement time of 640s.](/images/slow_strong_R_2f.svg){#fig:measurements:slow_strong_R_2f}

We have also performed a measurement at ambient CO~2~ concentrations using the same methodology. The $R\_{2/1}$ ratio of this measurement is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.38" >}}">6.38</a>. In this measurement, we can still clearly observe the 2f main and side lobes that were also visible in figure <a href="{{< ref "measurements_performance.md#figure-6.37" >}}">6.37</a>. Some minor additional lobes also arise between 200s and 430s, probably due to imperfections in the driver and detector. More interestingly, we also observe that the 2f amplitude goes up again near 300s. The source of this effect is unknown and is possibly non-linearity caused by the laser. Again, we also observe the errors introduced by the DAC.

![Measured ratio $R\_{2/1}$ after second redesign. Measurement in ambient CO~2~ levels using stepped bias modulation, resulting in a measurement time of 640s.](/images/slow_weak_R.svg){#fig:measurements:slow_weak_R}

# Re-evaluating Performance of the Initial Set-up With Increased Harmonic Suppression in the Laser Driver

Before finishing this measurement chapter, we revisit the measurement we performed in section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a>. A measurement at ambient CO~2~ levels is depicted in figure <a href="{{< ref "measurements_performance.md#figure-6.34" >}}">6.34</a>. The only difference between the measurement in section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a> and this section is the updated DAC that now produce less harmonics (the software side is exactly the same as in section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a>). 

From the ratio in figure <a href="{{< ref "measurements_performance.md#figure-6.34" >}}">6.34</a>, we can conclude that the system in section <a href="{{< ref "measurements_performance.md#section-6.1" >}}">6.1</a> suffered mostly from harmonics introduced by the bias DAC modulation. The measurement is now very similar to the one from figure <a href="{{< ref "measurements_performance.md#figure-6.38" >}}">6.38</a>, but with slight deviation between the main lobe amplitudes.

![Measurement with initial system design, but with updated DAC to suppress harmonics from the bias modulation. Measurement in ambient CO~2~ levels and averaged over 10 traces, resulting in an overall measurement time of 1s.](/images/original_update_weak_R.svg){#fig:measurements:original_update_weak_R}

# Conclusion

In this chapter, we measured the different electronic components and performed a set of initial gas concentration measurements. First, the electrical characteristics of most circuits is measured using transfer functions and then compared to the simulations from chapter <a href="{{< ref "laser_driver.md#chapter-3.1" >}}">3.1</a> and chapter <a href="{{< ref "detector.md#chapter-4.1" >}}">4.1</a>. If needed, the values are slightly tuned to achieve improved performance. 

Next, a set of gas concentration measurements is performed for high and low CO~2~ concentrations using the CO~2~ DFB laser. It is found that the driver creates too many harmonics that interfere with the 2f measurement. The system is subsequently redesigned twice to assess the cause and solutions for these problems.

The main issue is determined to be the harmonics generated by the driver. However, other issues such as influence of the TEC and driver/detector imperfections remain and require further investigation to achieve improved performance. 
