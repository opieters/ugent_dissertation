---
title: Introduction, Setting and Background
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 1
---

WMS is a technique whereby gas properties such as gas concentration and temperature can be extracted from light-matter interaction. The drive current (or temperature) of the laser diode is modulated with a sine signal around its bias value. This sine modulation will then cause a sine modulation of the emitted wavelength as well. Usually, the emitted power will also vary sinusoidally, but this modulation is not of interest in this measurement technique. This laser line interacts with the gasses present in the environment. The gas of interest should have a gas absorption peak within the wavelength range of this laser device. This absorption peak is also wavelength dependent and will result in non-linear transmission function, creating higher order harmonics of this sine wave at the detecting photodiode. The resulting light is incident on a photodiode. Using signal processing techniques, gas characteristics can be extracted. The construction of such a system forms the main goal of this dissertation.

In this chapter, we will highlight the typical components of a wavelength modulation spectroscopy system, their function and trade-offs in such a system. First, we will discuss the relevance of measuring gas concentration and more specifically, CO and CO~2~. Next, we will compare different techniques that can be employed to measure gas concentrations and finally we will give a high-level overview of the measurement principles.

# Importance of Gas Concentration Measurements

Gas concentration measurements have applications in various domains such as air quality, food packaging, agriculture and human health. These applications are strongly gas dependent. In this dissertation, we focus on CO and CO~2~ as gasses of interest.

CO~2~ gas sensing can be applied to monitor the ambient air quality. By means of such a measurement, ventilation can be turned on or windows opened if the CO~2~ concentration exceeds a certain level. Outdoor CO~2~ monitoring is also possible for instance in the vicinity of coal plants and other industrial operations that produce large amounts of CO~2~.

Another application domain is greenhouse crop production. Increased levels of CO~2~ can improve photosynthesis and improve crop growth. The optimal levels of CO~2~ are crop dependent, thus precise control is needed using accurate measurements [@nederhoff1994effects].

Modified atmospheres are often used in food packaging to preserve food longer. O2 concentrations are lowered while the concentration of CO~2~ is increased. An optical measurement can be used to verify package seal integrity. Another use of this measurement is to detect spoilage when higher than expected CO~2~ concentrations are detected [@CO2-food-preservation; @CO2-food-sensors].

CO is a poisonous gas for humans. CO monitoring in rooms with potentially dangerous levels is important to alert people and activate ventilation if necessary.

# Tuneable Diode Laser Absorption Spectroscopy

WMS is a well established technique for gas sensing and a special form of TDLAS. By means of TDLAS, gas properties such as concentration, temperature and flow rate can be determined [@WMSTemperature; @TDLASVelocity; @OpticalGasSensingReview].

In TDLAS, the frequency (wavelength) of a laser is tuned by changing one of its input variables. Typical tuning factors are temperature and input current. By means of this variable wavelength, we can scan across one (or more) absorption peaks. Absorption peaks are wavelength dependent phenomena observed a gas' spectrum. A laser is biased such that it emits light in the neighbourhood of this peak and by changing the input variables, the entire peak is scanned. Based on the amount of incident power on a photo diode versus the emitted power, gas properties (concentration, temperature, mass flux&hellip;) can be extracted with high accuracy [@OpticalGasSensingReview]. This is illustrated in [@fig:background:TDLAS_principle]. In this dissertation, we will only focus on measuring the gas concentration.

![Operating principle of a TDLAS system in case of current tuning. The input (either current or temperature) changes the wavelength of the emitted laser line (and the intensity also). This laser line scans over the absorption line. The amount of absorption is determined by gas parameters such as concentration, temperature and velocity. The presence of other gasses which have nearby absorption lines can also interfere with the measurement. The resulting incident light on the photodiode is thus dependent on these gas properties which can be extracted after careful measurement.](/images/TDLAS_principle.svg){#fig:background:TDLAS_principle}

The operating principle of TDLAS is straightforward. By means of the Beer-Lambert law, that quantifies the amount of light absorbed by the transmission medium, the transmitted power can be related to concentration:

\\[
I(\nu) = I\_0 \exp(-\alpha(\nu)L)
\\] {#eq:background:Beer-Lambert-law}

The incident light intensity \\(I\_0\\) is transmitted through a gas cell of length \\(L\\) (the length over which the light interacts with the gas) and absorbance $\alpha(\nu)$ with $\nu$ the wavenumber (\\(1/\lambda\\)). It is also assumed that the source is monochromatic (i.e. its optical bandwidth is much smaller than the bandwidth of the absorption lines) and that there are no chemical changes in the gas cell [@abs-spec-status-and-potential]. The absorbance \\(\alpha(\nu)\\) can be written as [@RiekerDissertation]:

\\[
\alpha(\nu) = \sum\_j S\_j(T) P\_i \phi\_j(\nu-\nu\_0)
\\] {eq:background:absorption-coefficient}

where \\(S\_j(T)\\) is the line strength (in cm<sup>-1</sup>) of absorbance feature \\(j\\), \\(P\_i\\) the partial pressure (dimensionless) of the gas of interest and \\(\phi(\nu)\\) the line shape profile (dimensionless and normalised to have unit amplitude at \\(\nu=0\\)). There are three line shape profiles: Lorentzian, Gaussian and Voigt. [@Eq:background:lorentzian; @eq:background:gaussian; @eq:background:voigt] are the normalised line shapes (\\(*\\) represents the continuous convolution) for the profiles. A graphical depiction is made in [@fig:background:line_profiles] where each of the line shapes is normalised.

\\[
\begin{align}
\text{Lorentzian profile} &\qquad & \phi\_L(\nu; \gamma) &= \dfrac{\gamma}{\pi\br{\gamma^2+\br{\nu-\nu\_0}^2}} {#eq:background:lorentzian}\\
\text{Gaussian profile}   &       & \phi\_G(\nu; \gamma) &= \sqrt{\dfrac{\ln{2}}{\pi}}\exp\br{-\ln{2}\br{\dfrac{\nu-\nu\_0}{\gamma}}^2} \tag{#eq:background:gaussian}\\
\text{Voigt profile}      &       & \phi\_V(\nu)         &= \phi\_L(\nu; \gamma\_L) * \phi\_G(\nu; \gamma\_V) {#eq:background:voigt}
\end{align}
\\] {:eq:background:line_shapes}

Important parameters of these line shapes are line width and line strength (maximal amplitude). The width is usually described in terms of the HWHM (\\(\gamma\\)) or FWHM (\\(2\gamma)\\). The appropriate line shape depends on which of the broadening mechanisms dominate.

A Lorentzian line shape is the result of lifetime/pressure broadening. Different molecular states have different lifetimes associated with them. Changes occur due to collisions (hence the name pressure broadening), emission and absorption. Following Heisenberg's principle, the finite lifetime of these levels results in an uncertainty of energy. This uncertainty results in an absorption line with non-zero width [@spectrochemical-analysis]. This absorption line can be assumed for high pressures and low flow rate.

The Gaussian line shape must be used in case Doppler broadening dominates. For Doppler broadening, the velocity of emitting and absorbing atoms along the absorption path varies according to a stochastic distribution. This difference in velocity results in atoms interacting with the laser line at different wavelengths. This broadens the line shape overall [@spectrochemical-analysis]. This absorption line can be used when the gas pressure is low and the flow rate high.

The Voigt profile is the convolution of the previous two line shapes. It has two width parameters: one from the Lorentzian line shape and one from the Gaussian line shape. This line shape must be used when both lifetime and Doppler broadening are important. When using this line shape, the assumption is made that both effects (pressure broadening and Doppler broadening) are independent [@misunderstanding-voigt; @spectrochemical-analysis]. This is the most general line shape, taking both pressure and flow rate broadening into account.

![Normalised Lorentzian, Gaussian and Voigt line shape profiles. The HWHM (\\(\gamma\\)) was set to 1 for all profiles.](/images/line_profiles.svg){#fig:background:line_profiles}

This concludes our discussion on the operating principles of TDLAS and the physical nature of the gas absorption lines that will be of importance in the rest of this dissertation. Now, we will briefly discuss why TDLAS is used and what types there are.

TDLAS has several advantages: a high signal-to-noise ratio, a high degree of specificity if a non-interfering region can be found, as is often the case (i.e. there exist no nearby absorption lines of gasses that can also be present in the gas cell), and fast measurement compared to other techniques such as non-dispersive gas sensing. A thorough review of different optical measurement techniques can be found in [@OpticalGasSensingReview].

Two forms of TDLAS exist: line scanning and WMS. In the case of line scanning, the laser is swept over an absorption peak. In the case of WMS, the input signal is not only swept over the feature, but an additional small-scale sine modulation is applied to the input signal. The frequency of this sine wave is typically much higher than the ramp of triangle that sweeps over the entire absorption feature. {@Fig:background:TLDAS-basic-line-scanning-schemes,background:TDLAS-basic-WMS-schematic} illustrate schematic setups of both methods.

## Line Scanning Absorption Spectroscopy {#sec:background:line-scanning-absorption-spectroscopy}

The easiest way to determine a gas concentration is by means of a line scanning absorption spectroscopy measurement. The input in [@fig:background:TDLAS_principle] is a ramping function (or triangle wave) that ideally only ramps the output wavelength while maintaining a constant output power. In practice, the output power is modulated too, as is visible in [@fig:background:direct-absorption-measurement]. This figure depicts a typical measurement with output power modulation. This power modulation result in a RAM at the detector that needs to be removed. [@Fig:background:TLDAS-basic-line-scanning-schemes] depicts the logical elements needed to perform this type of measurement.

In [@fig:background:TLDAS-basic-line-scanning-schemes], a computer simulation of a direct absorption spectroscopy measurement is depicted. A current ramp around the desired bias current is applied to the input of the laser. The laser emission wavelength will also have this ramp modulation and thus scan over the absorption feature. The light-matter interaction in the gas cell will cause different powers being detected by the photodiode than those emitted by the laser. The resulting photodiode output current will thus depend upon gas properties. An amplifier then amplifies the signal to suitable levels for digital conversion. Finally, the computer processes the data.

Typically, the concentration is extracted using a single point (the minimum due to the absorption peak) or by means of integration of the incident light. Using the law of Beer-Lambert ([@eq:background:Beer-Lambert-law]), the concentration is extracted [@OpticalGasSensingReview] after subtraction of the RAM. Since measurements are typically done in ambient conditions, the absorption peak will have an approximate Lorentzian line profile in [@eq:background:absorption-coefficient] [@spectrochemical-analysis].

The major advantage of this method is its simplicity. A single ramping (or triangle) function is needed to scan over the feature to find the dip resulting from absorption. Then the concentration is extracted. This simplicity does limit the resolution however. WMS typically has improved SNR due to its zero baseline and higher dependence on spectral features [@OpticalGasSensingReview].

![Typical line scanning absorption spectroscopy measurement with and without CO~2~ for a VCSEL laser (see table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a> for specifications) from 2002.3nm to 2002.8nm for a path length of 100cm and a CO~2~ concentration of 800ppm.](/images/direct-absorption-measurement.svg){#fig:background:direct-absorption-measurement}

![/images/TLDAS-basic-line-scanning-schemes.svg](Logical structure for line scanning TDLAS measurement system.){#fig:background:TLDAS-basic-line-scanning-schemes}

## Wavelength Modulation Spectroscopy {#sec:background:WMS}

For WMS, the laser is tuned with a slow and fast varying signal. A fast sinusoid (typically with a frequency of 1 to 100kHz) is superimposed on a low-frequency ramp (or triangle with a typical frequency of 1 to 100Hz), the sum of these two signals (and a biasing component) is then fed into the laser to tune the wavelength of the emitted laser line [@OSA-freq-int-modulation].

Each of these input currents have a specific function. The bias current sets the absorption line over which the ramp (or triangle) modulation scans. The ramp (or triangle) changes the biasing conditions of the laser and the fast sinusoid scans (partially) over the absorption line. In the subsequent, we will refer to this sinusoid as the first order harmonic.

The absorption line cause the transmission function to be non-linear. This will cause formation of different harmonics of the fast sinusoid in the incident light on the photodiode. Higher order harmonics are highly dependent on the spectral shape (as they are caused by the spectral line) and are thus ideal for gas property extraction. These can be detected using LIA. LIA can be seen as narrow band digital filtering structures.

[@Fig:background:WMS_absorption_plot] is a graphical simulation of such a measurement. As we can observe, the bias current set the output power of the wavelength to a mean value of about 0.2915mW, the ramp scans over the entire absorption line and the sine does so partially. The variation of the first order harmonic amplitude can also be clearly observed in the figure: constant amplitudes near 0.5s and 0.55s, but much larger amplitude to the left and right of the maximum absorption and almost zero at the minimum absorption. This is due to the shape of the absorption line. The second order harmonic is not visible here (since it's much smaller). But it can be made visible using LIA. The resulting amplitudes of the first and second order harmonics are depicted in [@fig:background:1f_2f_LIA].

The main advantage of this technique is improved SNR due to the usage of LIA. They allow to extract signals near the noise floor. Additionally, by using a high frequency sine (typically at least a 10kHz), the system can more easily escape the $1/f$ noise region. Higher order harmonics are also highly dependent on the absorption line, making them good candidates for concentration extraction. While the SNR can be improved, the extracted harmonics are typically quite weak (and increasingly so for higher order harmonics). The first and/or second order harmonics are usually used. These can for instance be combined to form a ratio. The concentration is extracted based on a mathematical model or simulations. 

Generally the 2f is used because this harmonic has high dependence on gas properties and is stronger than higher order (third, fourth&hellip;) harmonics. The first order harmonic may be used for normalisation. This removes laser drift, amplitude modulations and possible vibrations [@RiekerDissertation]. Care must be taken however, that the 1f signal does not become zero. This problem can be alleviated by means of _apodized 2f/1f wavelength modulation spectroscopy_ [@apodized-WMS]. This technique will not be explored further, since we focus on lower gas concentration measurements in this dissertation.

![Typical shape of a WMS spectroscopy measurement. A fast sinusoid is modulated (with modulation index of 2.2) on a slow ramp modulation that changes the bias conditions. The transmission power is depicted with and without CO~2~ for a VCSEL laser (see table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a> for specifications) from 2002.3nm to 2002.8nm for a path length of 100cm and a CO~2~ concentration of 800ppm.](/images/WMS_absorption_plot.svg){#fig:background:WMS_absorption_plot}

![Extracted first and second harmonics using LIA from measurement in figure <a href="{{< ref "background.md#figure-1.5" >}}">1.5</a>. The sine wave was set to obtain a modulation index of 2.2 (see section <a href="{{< ref "laser_driver.md#section-3.3" >}}">3.3</a> for a detailed description on why a value of 2.2 is ideal).](/images/1f_2f_LIA.svg){#fig:background:1f_2f_LIA}

[@Fig:background:1f_2f_LIA] depicts the first and second order harmonics extracted using LIA (the power of the simulated laser was set to a maximum of 10mW). This is the same simulation as the one from [@fig:background:WMS_absorption_plot]. The 1f is not centred around 0 because it is already present in the laser output light. However, this component usually does not pass through zero. However, in [@fig:background:1f_2f_LIA] this is the case because a large concentration was used to make the [@fig:background:WMS_absorption_plot] more clear.

The main disadvantage of this technique is that the harmonic amplitudes are usually quite low (especially the second order component, as seen in {@fig:background:1f_2f_LIA}). Additionally, the system complexity is much higher than for line scanning absorption spectroscopy ({#sec:background:line-scanning-absorption-spectroscopy; @fig:background:TDLAS-basic-WMS-schematic}).

![Logical structure of a WMS measurement system.](/images/TDLAS-basic-WMS-schematic.svg){#fig:background:TDLAS-basic-WMS-schematic}

# Overview of this Dissertation {#sec:background:overview}

In this section, we discuss the overall goal of this dissertation and the contents of the different chapters this document.

The main goal of this dissertation is to develop a small-scale prototype that enables to measure ambient gas concentrations of CO~2~. This is done using discrete off-the shelf comments and should alleviate the need for complex and expansive measurement equipement (e.g. oscilloscope, current source, digital acquisition card).

This system architecture is first discussed in [@sec:system]. The system can be subdivided into three pieces: the driver, detector and digital control part. Each of these has its own chapter: [@sec:laser_driver; @sec:detector, @sec:DCP]. These chapters highlight the design and implementation decisions. The problem is tackled in an as general as possible manner to ease adopting the system to another set of specifications more straightforward.

[@Sec:system] also discusses the specifications of the system. How the concentration is extracted after analogue processing, is discussed in [@sec:DCP]. All chapters make use of simulation to check performance. [@Sec:measurements_performance] makes the leap from simulation to realisation, discusses performance of each of the electrical components and also provides concentration measurements. Finally, a conclusion ([@sec:conclusion]) wraps everything up.
