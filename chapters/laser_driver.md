---
title: Laser Driver Design
date:   "2018-09-08"
tags: [Dissertation]
type: "chapter"
author: "Olivier Pieters"
description: "description"
keywords: ["key", "words"]
topics: ["topic 1"]
tags: ["one", "two"]
chapter: 3
---





In this chapter, we will design the laser driver. First, we select the biasing values and appropriate absorption peaks for gas concentration detection. Second, some considerations will be made with respect to analogue-to-digital conversion using different simulation approaches. Third, we will design analogue circuits to implement the different functions between the controller and laser in figure <a href="{{< ref "background.md#figure-1.2" >}}">1.2</a>. 

<h2 id="section-3.2">Absorption Feature Selection</h2>

Prior to starting the laser driver design architecture, absorption peaks need to be selected. We consider several laser devices: a <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> and <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser, both around 2004nm [<a href="{{< relref "#citation1" >}}">1</a>], and an array of <abbr title="Distributed Feedback (Laser)">DFB</abbr> lasers that were designed by Ruijun Wang from the Photonics Department of Ghent University [<a href="{{< relref "#citation2" >}}">2</a>]. Table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a> lists the most important laser specifications. Using these in combination with the absorption spectra of <abbr title="Carbon Monoxide">CO</abbr> and <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> (figure <a href="{{< ref "laser_driver.md#figure-3.5" >}}">3.5</a> and figure <a href="{{< ref "laser_driver.md#figure-3.4" >}}">3.4</a>), we will select the absorption peaks of interest.

Do note that in table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a>, the specifications of three different laser devices are summarised. Two of these can be used for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration measurements and the array of <abbr title="Distributed Feedback (Laser)">DFB</abbr> lasers can be used for the <abbr title="Carbon Monoxide">CO</abbr> concentration measurements. 



<figure id="figure-3.5">
<img alt="laser_driver:absorption_coefficient_CO_2" src="/images/absorption_coefficient_CO_2.svg">
<figcaption>
Figure 3.5: Absorption peaks of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> between 1990nm and 2040nm at 296K for a 100% <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration at 1atm [<a href="{{< relref "#citation3" >}}">3</a>].
</figcaption>
</figure>



<figure id="figure-3.4">
<img alt="laser_driver:absorption_coefficient_CO" src="/images/absorption_coefficient_CO.svg">
<figcaption>
Figure 3.4: Absorption peaks of <abbr title="Carbon Monoxide">CO</abbr> between 1990nm and 2040nm at 296K for a 100% <abbr title="Carbon Monoxide">CO</abbr> concentration at 1atm [<a href="{{< relref "#citation3" >}}">3</a>].
</figcaption>
</figure>

  

<table id="table-3.2">
<caption>Table 3.2: Laser specifications for <abbr title="Carbon Monoxide">CO</abbr> and <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> concentration measurements [<a href="{{< relref "#citation2" >}}">2</a>, <a href="{{< relref "#citation1" >}}">1</a>].</caption>
<tr>
<th></th>
<th><abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></th>
<th><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr></th>
<th><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></th>
</tr>
<tr>
<td>wavelength</td>
<td>2321.1 to 2326nm</td>
<td>2000 to 2007nm</td>
<td>1950 to 2150nm</td>
</tr>
<tr>
<td>input current</td>
<td>100 to 200mA</td>
<td>1.5 to 15.5mA</td>
<td>30 to 140mA</td>
</tr>
<tr>
<td>input voltage</td>
<td>1.5 to 3V</td>
<td>0 to 1.6V</td>
<td>0 to 1.6V</td>
</tr>
<tr>
<td>temperature</td>
<td><15C</td>
<td>20 to 40C</td>
<td>0 to 50C</td>
</tr>
<tr>
<td>current tuning rate</td>
<td>10pm/mA</td>
<td>440pm/mA</td>
<td>3pm/mA</td>
</tr>
<tr>
<td>resolution</td>
<td>1pm</td>
<td>-</td>
<td><0.026pm</td>
</tr>
<tr>
<td>temperature tuning</td>
<td>150pm/C</td>
<td>-</td>
<td>100pm/C</td>
</tr>
</table>


<h3 id="subsection-3.2">Absorption Feature Selection for CO<sub>2</sub></h3>

In this subsection, we select an optimal absorption peak for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> for the lasers considered. Initially, we selected the  <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>, but due to technical issues, this laser was later replaced by a <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser at 2004nm [<a href="{{< relref "#citation1" >}}">1</a>]. The absorption peak was initially only optimised with respect to the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>. However, as will be discussed in this subsection, the choices made for this laser can be readily be transferred to the <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser.

Figure <a href="{{< ref "laser_driver.md#figure-3.17" >}}">3.17</a> depicts the output power of the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> (fiber coupled output, thus this power is fully available) and the absorption coefficient of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> (zoomed version of figure <a href="{{< ref "laser_driver.md#figure-3.5" >}}">3.5</a> to wavelength range covered by laser). Additionally, the absorption coefficient for water, an important interferer, is plotted as well (upscaled with a factor of 100 for better visibility). A measurement typically performed by scanning the laser between two absorption minima. The peak (maximum) of the absorption peak forms the mean wavelength.

In order to select the best peak, we considered different <abbr title="Figure of Merit">FOM</abbr>. They are listed in equation <a href="{{< ref "laser_driver.md#equation-3.2" >}}">3.2</a>, equation <a href="{{< ref "laser_driver.md#equation-3.3" >}}">3.3</a>, equation <a href="{{< ref "laser_driver.md#equation-3.4" >}}">3.4</a>, equation <a href="{{< ref "laser_driver.md#equation-3.5" >}}">3.5</a> and equation <a href="{{< ref "laser_driver.md#equation-3.6" >}}">3.6</a>. In these equations, 
$\Delta\lambda$ represents the absorption feature peak width (from the adjacent minimum absorption on the left to the right side); 
$\alpha\_\text{peak}$ is the highest absorption coefficient of this peak, 
$\Delta i$ is the difference in input current of the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> to scan over the absorption peak; 
$I\_\text{peak}$ is the input current at maximum absorption ($\alpha\_\text{peak}$), 
$\overline{\alpha\_\text{H<sub>2</sub>O}}$ is the mean absorption coefficient of water over the absorption peak considered; 
$\overline{|\alpha'\_\text{H<sub>2</sub>O}|}$ is the mean of the derivative of the absorption coefficient of water over the absorption peak considered; and
$P\_\text{peak}$ is the output power of the laser source at the absorption coefficient peak.



<div>
$$
\begin{align}
FOM\_1 &= \frac{\Delta\lambda \alpha\_\text{peak} \Delta i}{I\_\text{peak}\overline{\alpha\_\text{H<sub>2</sub>O}}\cdot\overline{\alpha'\_\text{H<sub>2</sub>O}}} \tag{3.2}\\
FOM\_2 &= \frac{P\_\text{peak} \alpha\_\text{peak}}{\overline{\alpha\_\text{H<sub>2</sub>O}}} \tag{3.3}\\
FOM\_3 &= \frac{\Delta\lambda P\_\text{peak} \alpha\_\text{peak}}{\overline{\alpha\_\text{H<sub>2</sub>O}}} \tag{3.4}
\end{align}
$$
</div>



<div>
$$
\begin{align}
FOM\_4 &= \frac{\Delta\lambda P\_\text{peak} \alpha\_\text{peak}}{\overline{\abs{\alpha'\_\text{H<sub>2</sub>O}}}} \tag{3.5}\\
FOM\_5 &= \frac{P\_\text{peak} \Delta\lambda \alpha\_\text{peak} \Delta i}{I\_\text{peak}\overline{\alpha\_\text{H<sub>2</sub>O}}\cdot\overline{\abs{\alpha'\_\text{H<sub>2</sub>O}}}} \tag{3.6}
\end{align}
$$
</div>



These features capture absorption peak characteristics ($\Delta\lambda$, $\alpha\_\text{peak}$, $\overline{\alpha\_\text{H<sub>2</sub>O}}$ and $\overline{|\alpha'\_\text{H<sub>2</sub>O}|}$) and characteristics of the laser ($I\_\text{peak}$, $\Delta i$ and $P\_\text{peak}$). Ideally, the difference in input current should be considerable. This makes it easier to generate the ramp that scans over the absorption peak and sinusoidal modulation of this ramp. Errors due to component variation also become less important this way. However, the mean value of the input current ($I\_\text{peak}$) should remain limited to simplify the design of the the voltage-to-current converter. Furthermore, the second order non-linearity needs to be as strong as possible (proportional to $P\_\text{peak}$ and $\alpha\_\text{peak}$) while keeping interference from water low ($\overline{\alpha\_\text{H<sub>2</sub>O}}$) or at least as constant as possible ($\overline{|\alpha'\_\text{H<sub>2</sub>O}|}$). 

After computation of each of these <abbr title="Figure of Merit">FOM</abbr> (the laser is assumed to operate at 25C), normalisation was applied to obtain a dimensionless figure. Figure <a href="{{< ref "laser_driver.md#figure-3.18" >}}">3.18</a> is a graphical representation of the <abbr title="Figure of Merit">FOM</abbr> for each of the absorption peaks in the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> wavelength range. It is clear, that the absorption peak at 2003.5nm has the best performance for each of the <abbr title="Figure of Merit">FOM</abbr>. In case we are using the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser, this peak can also be covered. The optimal peak for this laser will also be the same because the replacement <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser has a very wide wavelength range (table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a>). Due to the limited variation in wavelength when using current tuning, there is no option to take the bias point into account in the <abbr title="Figure of Merit">FOM</abbr>. Consequently, the optimal absorption peak for the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> is the same as that for the <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> measurements since this peak is optimal for all <abbr title="Figure of Merit">FOM</abbr>.

This <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser is very sensitive to temperature variations (see table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a>), as a consequence, the <abbr title="Thermoelectric Cooling">TEC</abbr> will need to set the correct biasing point since the wavelength variation by means of current variation is limited. A <abbr title="Thermoelectric Cooling">TEC</abbr> controller keeps the laser temperature fixed by either cooling it or heating it up by means of a current. This temperature is usually set by reading the value of a thermistor. The <abbr title="Thermoelectric Cooling">TEC</abbr> should be tuned such that it keeps the lasers temperature around the biasing point. However, it must not attempt to settle on any of the modulations we will use in the driver (see chapter <a href="{{< ref "laser_driver.md#chapter-3.1" >}}">3.1</a>). These modulations will vary the input current. As a result, the laser temperature will also vary, but on average it should maintain the same temperature, set by selecting an appropriate thermistor value. If the <abbr title="Thermoelectric Cooling">TEC</abbr> is too fast (i.e. follows part of these modulations), then it will try to compensate for this slight variation in temperature. As a result, the wavelength modulation amplitude will be smaller.



<figure id="figure-3.17">
<img alt="laser_drivier:CO_2_absorption_peak_selection" src="/images/CO_2_absorption_peak_selection.svg">
<figcaption>
Figure 3.17: Wavelength vs. output power of the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr>, $\alpha\_\text{H<sub>2</sub>O}$ (left axis) and absorption coefficient of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> and <abbr title="Water">H2O</abbr> (right axis).
</figcaption>
</figure>



<figure id="figure-3.18">
<img alt="laser_drivier:FOM_CO_2" src="/images/FOM_CO_2.svg">
<figcaption>
Figure 3.18: FOM (left axis) for the different absorption peaks in the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> wavelength range. The absorption coefficient (right axis) is also plotted for convenience.
</figcaption>
</figure>



The most important properties of the selected peak values are listed in table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a>.



<h3>Absorption Feature Selection for CO</h3>

In the case of <abbr title="Carbon Monoxide">CO</abbr>, the spacing between different absorption peaks is larger (figure <a href="{{< ref "laser_driver.md#figure-3.4" >}}">3.4</a>). Additionally, the tuning range for amount of current tuning is much lower compared to the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> (table <a href="{{< ref "laser_driver.md#table-3.2" >}}">3.2</a>). The <abbr title="Distributed Feedback (Laser)">DFB</abbr> lasers available are designed by Ruijun Wang of the Photonics Research Group, Ghent University-imec [<a href="{{< relref "#citation2" >}}">2</a>]. Several devices are available, two of them are depicted in figure <a href="{{< ref "laser_driver.md#figure-3.2" >}}">3.2</a>. 


<figure id="figure-3.2">
<img alt="laser_driver:CO_laser_abs_cmp" src="/images/CO_laser_abs_cmp.svg">
<figcaption>
Figure 3.2: Spectra of 2 in-house DFB lasers superimposed on the CO absorption spectrum.
</figcaption>
</figure>



From figure <a href="{{< ref "laser_driver.md#figure-3.2" >}}">3.2</a>, we see that the 2324nm <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser at 5C is most favourable because it completely covers the strong absorption line. Additionally, the absorption line is in a stable region for laser operation.

<table id="table-3.3">
<caption>Table 3.3: Important absorption line characteristics.</caption>
<tr>
<th>C{1.5cm}C{1.5cm}C{3cm}C{2.5cm}}</th>
<th>$\lambda\_\text{min} [nm]$</th>
<th>centre wavelength $\lambda\_\text{c}$ [nm]</th>
<th>$\lambda\_\text{max}$ [nm]</th>
<th>peak absorption strength $\alpha\_\text{max}$ [/cm]</th>
<th>half peak half width $\Delta\lambda\_{1/2}$ [nm]</th>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr></td>
<td>2003.25</td>
<td>2003.50</td>
<td>2003.78</td>
<td>0.1012</td>
<td>0.0411</td>
</tr>
<tr>
<td><abbr title="Carbon Monoxide">CO</abbr></td>
<td>2324.39</td>
<td>2325.21</td>
<td>2326.00</td>
<td>0.3297</td>
<td>0.0343</td>
</tr>
</table>


For the selected absorption peak, there is no issue with absorption by water due to the absence of strong <abbr title="Water">H2O</abbr> peaks. However, if large concentrations of <abbr title="Methane">CH4</abbr> are present, a different peak needs to be selected. This does not form a problem for measurements in free space, because the concentration of methane is much lower than that of <abbr title="Carbon Monoxide">CO</abbr> [<a href="{{< relref "#citation4" >}}">4</a>].



<figure id="figure-3.1">
<img alt="laser_driver:CO_DFB_drive" src="/images/CO_DFB_drive.svg">
<figcaption>
Figure 3.1: Relationship between current and wavelength for <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser (left axis) (2324nm laser at 5C). The absorption coefficient of <abbr title="Carbon Monoxide">CO</abbr> is also depicted (right axis).
</figcaption>
</figure>



The input current-output wavelength relationship of the selected DFB laser is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.1" >}}">3.1</a>. The operating region is defined as the wavelength region where the output power is at least -30dBm. Table <a href="{{< ref "laser_driver.md#table-3.3" >}}">3.3</a> summarises the the most important characteristics of the absorption peaks. Using these and the current-wavelength relationships for the different lasers, we can define the amplitudes of the different signals in the next section. 

<h2 id="section-3.3">Input Current Signals</h2>

The input signal consists of three parts: a <abbr title="Direct Current">DC</abbr> bias signal, a low frequency ramp or triangle wave and a high frequency sine wave. The bias and low frequency triangle (ramp) are determined by the peak selection in the previous section. We will compute them in the subsequent. The amplitude of the sine wave will be chosen to maximise the second order harmonic. 

We will use a triangle wave to scan over the absorption feature. Triangle waves have less pronounced non-linearity (a ramp has a very steep transition). This will result in less harmonics that might interfere with the first and second order sine harmonics.

The bias signal is determined by the selected absorption peak: the peak centre's wavelength $\lambda\_c$, corresponds to a certain input current. This is the <abbr title="Direct Current">DC</abbr> bias current. The amplitude of the triangle waveform is determined as the deviation needed to scan over the absorption peak. This current is thus the deviation of the <abbr title="Direct Current">DC</abbr> bias signal needed to get to the two minima separating the selected absorption peak from the neighbouring ones. 

The choice for the sine wave component is done by sweeping the modulation index of the wavenumber modulation. The modulation index is defined in equation <a href="{{< ref "laser_driver.md#equation-3.9" >}}">3.9</a>. Here, $a$ is the modulation amplitude of the wavenumber due to the input current sine wave: ${\mean{\nu}\_0 + a\cos\br{\omega t}}$. $\mean{\nu}\_0$ is the mean wavenumber that varies slowly due to the ramp function. $\Delta\nu\_{1/2}$ is the <abbr title="Half-Width-Half-Maximum">HWHM</abbr>.

<div>
$$

m = \dfrac{a}{\Delta\nu\_{1/2}}
\tag{3.9}

$$
</div>


A modulation index $m$ of 2.2 results in maximal amplitude of the second order harmonic. This value is a well known optimum found in literature [<a href="{{< relref "#citation5" >}}">5</a>]. It was also verified through simulation. Figure <a href="{{< ref "laser_driver.md#figure-3.12" >}}">3.12</a> depicts the modulation index of the sine wave versus the normalised peak height of the <abbr title="Second Order Harmonic">2f</abbr> component. The optimal value from this simulation is $2.13\approx2.2$.  

The difference between the simulated optimal modulation index and the one reported in literature is due to the nature of the simulation: this was performed using the actual absorption coefficient of <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> at the 2003.5nm absorption line and not an ideal isolated peak. Many weaker absorption peaks are present as is visible in figure <a href="{{< ref "laser_driver.md#figure-3.17" >}}">3.17</a> (this is well visible around the 2005.64nm peak).



<figure id="figure-3.12">
<img alt="laser_driver:optimal_modulation_index" src="/images/optimal_modulation_index.svg">
<figcaption>
Figure 3.12: Normalised second order harmonic peak value for different modulation indices $m$ for the selected <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> peak at 2003.5nm.
</figcaption>
</figure>



With the information above, we can determine the needed amplitudes for the different lasers using the characteristics of the selected absorption peaks. We will provide full calculations for the <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> laser. Similar reasonings are valid for the other lasers. A summary of the different amplitudes is provided in table <a href="{{< ref "laser_driver.md#table-3.5" >}}">3.5</a>.

The centre wavelength of the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> peak is 2003.5nm. This corresponds to a wavenumber of 4991.3/cm. The laser emits light at this wavelength if the input current is tuned to 9.759mA. This is the <abbr title="Direct Current">DC</abbr> bias current. This peak has a <abbr title="Half-Width-Half-Maximum">HWHM</abbr> of 0.0411nm, which results in a wavelength tuning range of 0.0904nm for the sine wave. To obtain this wavelength variation, we need to use a sinusoidal current with an amplitude of 0.185mA. The minima between this peak and the adjacent ones are located at 4990.6/cm and 4991.9/cm. A triangle wave of 0.571mA needs to be applied to scan between these minima. A similar reasoning is applicable for the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser. The triangle current is limited to 30mA instead of the ideal value of 49.973mA to remain within the operating region. As a consequence, we might not be able to scan over the entire absorption peak and as such not be able to detect the entire <abbr title="First Order Harmonic">1f</abbr> and <abbr title="Second Order Harmonic">2f</abbr> components, depicted in figure <a href="{{< ref "background.md#figure-1.1" >}}">1.1</a>. The relationship between wavenumber (wavelength) and input current needs to be established experimentally or extracted from the datasheet. 

The <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser has limited wavelength-current tuning capabilities (experimentally determined as 4.3pm/mA). As a result, the mean value between the operating region (70mA) is set as <abbr title="Direct Current">DC</abbr> bias current. The centre wavelength needs to be tuned by setting the appropriate operating temperature. The triangle amplitude needs to be limited as well since an ideal value would result in a triangle current amplitude of 61.477mA. This value cannot be realised since there is no laser operation below 30mA and the maximum input current is rated at 140mA. The practical limit is 19mA [<a href="{{< relref "#citation1" >}}">1</a>]. The sine amplitude was determined using the method described above.

<table id="table-3.5">
<caption>Table 3.5: Drive signal amplitude overview.</caption>
<tr>
<th>C{3.5cm}}
laser</th>
<th><abbr title="Direct Current">DC</abbr> bias [mA]</th>
<th>triangle wave amplitude [mA]</th>
<th>sine wave amplitude [mA]</th>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr></td>
<td>9.759</td>
<td>0.571</td>
<td>0.185</td>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>70</td>
<td>19</td>
<td>21.078</td>
</tr>
<tr>
<td><abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>194.398</td>
<td>30</td>
<td>9.277</td>
</tr>
</table>


Do note that the amplitudes in table <a href="{{< ref "laser_driver.md#table-3.5" >}}">3.5</a> are _not_ peak-to-peak values. For example, the sine wave for the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser will thus vary between -9.277mA and 9.277mA (relative to the bias value). 

Before starting the design of the signal generators, we need to fix the frequencies of the different signals. The frequencies are selected as follows: the line scanning triangle signal will vary at 10Hz and the sine wave at 10kHz. These values were co-designed with the <abbr title="Transimpedance Amplifier">TIA</abbr> such that the <abbr title="Transimpedance Amplifier">TIA</abbr> bandwidth is optimally used. These frequencies are spaced sufficiently far apart for easy removal of the triangle wave. Additionally, both frequencies are not too high such that we can easily generate them using a dedicated circuit. The sine-wave should also escape the $1/f$ noise for most circuits. The main disadvantage of these values is the need for active filtering because component values become too large for passive filtering with a low cut-off frequency; especially higher order filtering becomes difficult. Furthermore, increasing the frequencies by a decade or more complicates the digital processing considerably, since we need to sample at sufficiently high rates and accuracy (four times the <abbr title="Second Order Harmonic">2f</abbr> frequency, as we will see in chapter <a href="{{< ref "digital_control_processing.md#chapter-5.1" >}}">5.1</a>).

<h2 id="section-3.4">Signal Generation Techniques</h2>

In this section we will consider different signal generation techniques for the triangle and sine waves. We will select the most suitable drive techniques for both signals. The considered techniques are: analogue generation, digital generation with low pass filtering, and digital generation without filtering. A simulation based analysis is made on an idealised model to compare different approaches.

<h3>Analogue Generation</h3>

Analogue generation requires design of individual circuits for each of the variable input signals: the sine and triangle waves. We will study voltage signal generation here, a voltage controlled current source can be used to convert the voltage signal into a current signal to drive the laser.

A circuit that generates a triangle wave signal is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.14" >}}">3.14</a>. It is assumed both amplifiers can operate rail-to-rail for both in- and output. Component sizing is done using equation <a href="{{< ref "laser_driver.md#equation-3.11" >}}">3.11</a> and equation <a href="{{< ref "laser_driver.md#equation-3.10" >}}">3.10</a> [<a href="{{< relref "#citation6" >}}">6</a>]. It is assumed the reference signal is half of the supply voltage ($V\_\text{ref} = \sfrac{1}{2}V\_\text{CC}$). $V\_\text{out}$ is the output triangle signal, centred around the bias value of $V\_\text{ref}$. $f\_\text{T}$ is the triangle period. 

<div>
$$
\begin{align}
V\_\text{out} &= \br{1+\dfrac{R\_1}{R\_2}}V\_\text{ref}
\tag{3.11}\\
f\_\text{T} &= \dfrac{R\_2}{4 R\_1 R\_3 C\_\text{I}} \tag{3.10}
\end{align}
$$
</div>




<figure id="figure-3.14">
<img alt="laser_driver:triangle_generator_circuit" src="/images/triangle_generator_circuit.svg">
<figcaption>
Figure 3.14: Triangle wave generator [<a href="{{< relref "#citation6" >}}">6</a>].
</figcaption>
</figure>



Similar techniques exist for sine-wave generation [<a href="{{< relref "#citation7" >}}">7</a>]. While attractive in theory, these techniques are not practical. The oscillation frequency will change due to component and temperature variation and non-idealities; and drift of the amplifiers. This is undesirable for good lock-in behaviour since the signal should be phase locked to the digital reference. Averaging over longer time periods is also more difficult. The generated frequency will never be exactly 10Hz and even drift slightly.

<h3 id="subsection-3.3">Digital Generation with Low Pass Filtering</h3>

An alternative for analogue generation is digital generation. A quantised approximation of the ideal analogue is synthesised by a <abbr title="Digital-to-Analogue Converter">DAC</abbr>. As a result, a stair-case approximation is made of the actual signal. Important design parameters are the number of levels, how fast levels can transition and (optionally) filtering to obtain a more ideal output waveform. These will be investigated in this subsection and subsection <a href="{{< ref "laser_driver.md#subsection-3.4" >}}">3.4</a>. 

We will assume a simple MATLAB model to determine the effect of quantised inputs on the resulting concentration measurements. To this end, we suppose the system depicted in figure <a href="{{< ref "laser_driver.md#figure-3.7" >}}">3.7</a>. The detector is assumed to be ideal (i.e. no noise, non-linearity and quantisation). The component of interest is the <abbr title="Digital-to-Analogue Converter">DAC</abbr>. It is assumed that the <abbr title="Digital-to-Analogue Converter">DAC</abbr> directly drives the laser since the voltage-to-current converter is only a conversion step that can be left out in simulation. Using these simulations, we can get an idea of the number of bits required. At this stage, we cannot yet assess the noise generated by the circuit, so we will not model this here. 



<figure id="figure-3.7">
<img alt="laser_driver:digital_filtering_simulation" src="/images/digital_filtering_simulation.svg">
<figcaption>
Figure 3.7: Overview of simulation components to compare different signal generation techniques.
</figcaption>
</figure>



Now, we will look at triangle and sine wave generation using a combination of an ideal and infinitely fast <abbr title="Digital-to-Analogue Converter">DAC</abbr> and low-pass filter. 

<h4>Triangle Wave</h4>

A 10Hz filtered triangle wave is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.22" >}}">3.22</a> and its spectrum in figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a>. Triangle signals need higher order harmonics (indicated by the spectrum in figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a>) to obtain their ideal shape. This makes filtering more complicated. The needed harmonics will be present, but their amplitude will be incorrect. Correctly scaling them from the base frequency with decreasing amplitude is not easy. This will decrease the <abbr title="Signal-to-Noise Ratio">SNR</abbr> of the triangle wave because a simple low pass filter does not suffice to obtain this desired spectrum. The filter used here is a third order Bessel filter with a constant group delay up to $3.5f\_\text{triangle}$. We opted to use this filter type because of its linear phase, which helps to approximate the ideal triangle. This filter results in the highest <abbr title="Signal-to-Noise Ratio">SNR</abbr> (22.97dB for the Butterworth filter and 27.55dB for the Bessel filter, both after delay cancellation). Only optimised Bessel and Butterworth filters are considered here. The cut-off frequency of the filter in figure <a href="{{< ref "laser_driver.md#figure-3.22" >}}">3.22</a> and figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a> was set to $2.5f\_\text{triangle}$.

One of the harmonics from the triangle wave, whose spectrum is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a>, might fall on the second order harmonic. This is not really a problem since its amplitude is sufficiently low, as indicated by the spectrum.



<figure id="figure-3.22">
<img alt="laser_drivier:digital_triangle_time" src="/images/digital_triangle_time.svg">
<figcaption>
Figure 3.22: Quantised triangle signal, with and without filtering (using a second order Bessel and Butterworth filter). Filtering using a Bessel filter achieves higher signal to noise ratio (27.55dB compared to 22.97dB after delay compensation.
</figcaption>
</figure>



<figure id="figure-3.21">
<img alt="laser_drivier:digital_triangle_spectrum" src="/images/digital_triangle_spectrum.svg">
<figcaption>
Figure 3.21: Spectrum of different sine signals from figure <a href="{{< ref "laser_driver.md#figure-3.22" >}}">3.22</a>.
</figcaption>
</figure>



<h4>Sine Wave</h4>

Generating a sine wave should be easier to generate than a triangular wave, because the spectrum of the ideal signal has only one component at the frequency of interest. This makes both filtering and generation easier. At least two bits should be used. Otherwise, very high constraints are imposed on the filter design due to the amplitude of the harmonics. Here, a Butterworth filter of second order was used with a cut-off frequency of $1.7f\_\text{sine}$ where $f\_\text{sine}$ is 10kHz. $1.7f\_\text{sine}$ is used and not $1f\_\text{sine}$ to limit the attenuation of the (desired) sine frequency. The resulting SNR is 28.1dB. If a third order filter were used, the SNR would increase to 41.6dB. A first order filter results in an SNR that is too low to use because it is unable to properly attenuate high frequency components.

An interesting feature that can be observed from figure <a href="{{< ref "laser_driver.md#figure-3.19" >}}">3.19</a> is that, there is no second order component present.



<figure id="figure-3.20">
<img alt="laser_drivier:digital_sine_time" src="/images/digital_sine_time.svg">
<figcaption>
Figure 3.20: Ideal and quantised sine signal with and without filtering (using 2nd order Butterworth filter). The SNR between the ideal signal and the filtered signal is approximately 28dB (after delay correction).
</figcaption>
</figure>



<figure id="figure-3.19">
<img alt="laser_drivier:digital_sine_spectrum" src="/images/digital_sine_spectrum.svg">
<figcaption>
Figure 3.19: Spectrum of different sine signals from figure <a href="{{< ref "laser_driver.md#figure-3.20" >}}">3.20</a>. Notice that there is no second order harmonic present.
</figcaption>
</figure>



<h3 id="subsection-3.4">Digital Generation without Filtering</h3>

The non-filtered digital counterparts of the previous section are also depicted in figure <a href="{{< ref "laser_driver.md#figure-3.22" >}}">3.22</a>, figure <a href="{{< ref "laser_driver.md#figure-3.21" >}}">3.21</a>, figure <a href="{{< ref "laser_driver.md#figure-3.20" >}}">3.20</a> and figure <a href="{{< ref "laser_driver.md#figure-3.19" >}}">3.19</a>. This technique is more simple to implement at the cost of more harmonics in the input signal. The filtering will now be determined by the finite bandwidth of the operational amplifiers at the input stage. Additional non-linear effects due to finite slew rate will also occur. The slew rate should be select such that the voltage is reached before the next transition for any of the possible signal transitions. This is a complex non-linear effect and was not modelled in simulations.

The main advantage of digital generation over analogue generation is synchronisation. In case of digital generation, we are sure the input signal's amplitude or frequency are perfectly synchronised with the measurements of the <abbr title="Lock-In Amplifier">LIA</abbr>. This is not the case for analogue signals. A feedback system can compensate for drift in analogue implementations and digital filtered implementations at the cost of a higher complexity.

Additionally, analogue generation techniques suffer from drift due to ageing, temperature and other external influences. These can change the frequencies (and amplitudes) at which the circuits operate. This can possibly also result into problems for proper lock-in operation and long-time averaging. 

<h3 id="subsection-3.5">Driver Signals</h3>

In the previous part, generation techniques were investigated, now we will select on of these for each of our three signals: bias, triangle and sine waves. Each has different requirements, as discussed next. 

<h4>Bias</h4>

The bias signal should set the operating point of the laser with respect to the absorption feature. After averaging, the entire output signal, the signal should be centred on the absorption peak. The bias signal will not change in time, a simple resistive voltage divider will thus suffice to set the bias point. As a consequence, we will employ analogue generation (using a resistive divider) for the bias signal.

<h4>Triangle Signal</h4>

The triangle signal will enable scanning over the absorption line. Accurate setting of its frequency and (to a lesser extent) amplitude are required such that we scan using a fixed frequency, which is important for averaging in time during the digital processing phase. As a consequence, analogue generation is not suited for the triangle wave generation. Filtering complicates the setup, while not enabling us to accurately construct a triangle signal. Furthermore, the staircase triangle suffices to obtain the desired first and second order harmonic signals. A comparison between both is made in figure <a href="{{< ref "laser_driver.md#figure-3.15" >}}">3.15</a>. The simulation model is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.7" >}}">3.7</a> and only models an imperfect triangle signal. In this simulation, background subtraction was utilised to remove spurious components. Background subtraction tries to compensate for non-linearities in the system that are not the result of the gas absorption feature. This can greatly improve concentration extraction. More information on background subtraction can be found in section <a href="{{< ref "digital_control_processing.md#section-5.3" >}}">5.3</a>.

From figure <a href="{{< ref "laser_driver.md#figure-3.15" >}}">3.15</a> it is clear that we can omit complex filtering without seriously disturbing the signal. As a consequence, we will not use a second order Bessel filter in the setup, but use direct digital generation without filtering. According to simulation results, 3bits should suffice to obtain good results. In this first design however, we will employ 6bits in the actual generation. Later, the number of bits can easily be reduced without requiring redesign or fabrication of a new prototype.



<figure id="figure-3.15">
<img alt="laser_driver:triangle_type_influence_R" src="/images/triangle_type_influence_R.svg">
<figcaption>
Figure 3.15: Ratio $R=2f/1f$ of first and second order harmonic signals after background subtraction using ideal input, 3bit quantised triangle signal with (q+f) and without filtering (q).
</figcaption>
</figure>



<figure id="figure-3.13">
<img alt="laser_driver:sine_type_influence_R" src="/images/sine_type_influence_R.svg">
<figcaption>
Figure 3.13: Ratio $R=2f/1f$ of first and second order harmonic signals after background subtraction using quantised triangle input without filtering, 3bit quantised sine signal with (q+f) and without filtering (q).
</figcaption>
</figure>



<h4>Sine Signal</h4>

The sine signal has similar requirements as the triangle signal, but synchronisation and amplitude correctness are even more important for proper lock-in operation. Following the same reasoning, we will also employ direct digital generation without filtering. Again, this does not have major complications on the input signal, as is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.13" >}}">3.13</a>. According to simulation results, 2bits should suffice to obtain good results. In this first design however, we will employ 6bits in the actual sine wave generation. Later, the number of bits can easily be reduced without requiring redesign or fabrication of a new prototype.

<h2 id="section-3.1">Digital to Analogue Converter</h2>

The digital waveform needs to be converted to an analogue signal. This is done using a <abbr title="Digital-to-Analogue Converter">DAC</abbr>. A typical $R$-$2R$ <abbr title="Digital-to-Analogue Converter">DAC</abbr> circuit is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.3" >}}">3.3</a>. We will use two of these circuits to create a (quantised) sine and triangle signal. These will then be added using an analogue adder circuit, depicted in figure <a href="{{< ref "laser_driver.md#figure-3.6" >}}">3.6</a>, along with the bias value.



<figure id="figure-3.3">
<img alt="laser_driver:R-2R_ladder_DAC" src="/images/R-2R_ladder_DAC.svg">
<figcaption>
Figure 3.3: $R$-$2R$ ladder DAC
</figcaption>
</figure>





<figure id="figure-3.6">
<img alt="laser_driver:analogue_adder_DAC" src="/images/analogue_adder_DAC.svg">
<figcaption>
Figure 3.6: Analogue adder circuit. Combines bias, triangle and sine voltage signals into a single signal.
</figcaption>
</figure>



<h3>Circuit Analysis</h3>

The DAC circuit in figure <a href="{{< ref "laser_driver.md#figure-3.3" >}}">3.3</a> is a standard $R$-$2R$ ladder circuit that is buffered using an operational amplifier. This amplifier should have low offset voltage to ensure that the output voltage is close to the ideal value. Furthermore, sufficient bandwidth should be available to allow the desired signal to pass to the next stage. Assuming an ideal op-amp, the output voltage is given by:

<div>
$$

v\_\text{out} = \sum\_{i=0}^{N-1}\dfrac{2^i}{2^N}v\_i
\tag{3.1}

$$
</div>


In the actual circuit, a value of 6 is used for $N$, resulting in up to 64 different output levels for both triangle and sine wave. This should be sufficient when taking the analysis from section <a href="{{< ref "laser_driver.md#section-3.3" >}}">3.3</a> into account. The amplifier will be powered by a differential supply of $\pm5V$ while the sources $v\_i$ are output pins of the microcontroller, providing a voltage of up to 3.3V ($V\_{CC}$). The output voltage $v\_\text{out}$ will thus swing between 0 and  and $\sfrac{63}{64}V\_\text{CC}$. 

The adder circuit from figure <a href="{{< ref "laser_driver.md#figure-3.6" >}}">3.6</a> will combine and rescale the different input voltages to the output stage. This will then form the input for the next stage, the voltage controlled current source. The output voltage is:

<div>
$$
\begin{align}
v\_\text{out} & = -V\_{-}\dfrac{R\_\text{F}}{R\_\text{bias}} - v\_\text{triangle}\dfrac{R\_\text{F}}{R\_\text{triangle}} - v\_\text{sine}\dfrac{R\_\text{F}}{R\_\text{sine}} \\
& = V\_{+}\dfrac{R\_\text{F}}{R\_\text{bias}} - v\_\text{triangle}\dfrac{R\_\text{F}}{R\_\text{triangle}} - v\_\text{sine}\dfrac{R\_\text{F}}{R\_\text{sine}}
\tag{3.8}
\end{align}
$$
</div>


<h3 id="subsection-3.1">Component Selection and Error Analysis</h3>

In this section, we will size the actual components and select suitable amplifiers for the circuits from figure <a href="{{< ref "laser_driver.md#figure-3.3" >}}">3.3</a> and figure <a href="{{< ref "laser_driver.md#figure-3.6" >}}">3.6</a>. As resistor value $R$, we will select 1.5kΩ. Both values (1.5kΩ and 3kΩ) are available in the common E24 series. We will require the resistors to have an accuracy of at least 1%. This results in an accuracy of 1.65\pm0.015V for the most significant bit. This is sufficient, since the voltage rails are not perfect either. The typical error of supplies such as Keysight E3648A is 0.05 to 0.1% [<a href="{{< relref "#citation8" >}}">8</a>].

The <abbr title="Digital-to-Analogue Converter">DAC</abbr> for the sine and triangle waves have same output range. The resistor fractions $R\_\text{F}/R\_\text{sine}$ and $R\_\text{F}/R\_\text{triangle}$ will determine the actual scale. The current amplitudes have already been defined in table <a href="{{< ref "laser_driver.md#table-3.5" >}}">3.5</a>. The resistor values are designed in conjunction with the resistor $R\_\text{c}$ from figure <a href="{{< ref "laser_driver.md#figure-3.16" >}}">3.16</a> and table <a href="{{< ref "laser_driver.md#table-3.1" >}}">3.1</a> and listed in table <a href="{{< ref "laser_driver.md#table-3.4" >}}">3.4</a>. These resistor values have been selected since they result in current amplitudes that are as close as possible to the desired current amplitudes from table <a href="{{< ref "laser_driver.md#table-3.4" >}}">3.4</a>. If more accurate values are required, parallel resistor combinations can be used. These are denoted between brackets in table <a href="{{< ref "laser_driver.md#table-3.4" >}}">3.4</a>. In the prototype, we will only make use of a parallel resistor combination for the bias setting, since this is the most critical value.

<table id="table-3.4">
<caption>Table 3.4: Resistor values for analogue adder circuit.</caption>
<tr>
<th>laser</th>
<th>$R\_\text{F}$ (kΩ)</th>
<th>$R\_\text{bias}$ (kΩ)</th>
<th>$R\_\text{triangle}$ (kΩ)</th>
<th>$R\_\text{sine}$ (kΩ)</th>
</tr>
<tr>
<td><abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr> (<abbr title="Carbon Dioxide">CO<sub>2</sub></abbr>)</td>
<td>1.8</td>
<td>2  ($3.3\parallel51$)</td>
<td>180 ($200\parallel3600$)</td>
<td>510 ($560\parallel5600$)</td>
</tr>
<tr>
<td><abbr title="Distributed Feedback (Laser)">DFB</abbr> (<abbr title="Carbon Dioxide">CO<sub>2</sub></abbr>)</td>
<td>3.3</td>
<td>3.9 ($3.9\parallel3900$)</td>
<td>22 ($22\parallel68$)</td>
<td>20  ($20\parallel8.2$)</td>
</tr>
<tr>
<td><abbr title="Distributed Feedback (Laser)">DFB</abbr> (<abbr title="Carbon Monoxide">CO</abbr>)</td>
<td>7.5</td>
<td>11 ($11 \parallel910$)</td>
<td>82 ($82\parallel4700$)</td>
<td>270 ($300\parallel2.7$)</td>
</tr>
</table>


We selected the LTC1152 as amplifier for the designs in  figure <a href="{{< ref "laser_driver.md#figure-3.3" >}}">3.3</a> and figure <a href="{{< ref "laser_driver.md#figure-3.6" >}}">3.6</a>. This is a rail-to-rail input and output device, with shutdown pin. Furthermore, it is a zero-drift amplifier. As a result, it has a typical offset voltage of 1μV and temperature drift of 10nV/C, which should be sufficient to provide accurate <abbr title="Direct Current">DC</abbr> bias settings. Additionally, the amplifier also features a compensation/bandwidth limiting pin. This allows to obtain some first-order filtering without the need for an active filter. Finally, the slew-rate of this amplifier is 0.5V/μs. This should be sufficient for the application considered here [<a href="{{< relref "#citation9" >}}">9</a>].

The gain of the <abbr title="Digital-to-Analogue Converter">DAC</abbr> circuit is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.9" >}}">3.9</a> and features a phase margin of 66.5°. The gain of the adder circuit is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.11" >}}">3.11</a> and features a phase margin of 78.6°, both are above the recommended minimal phase margin of 60°. Both circuits utilise a compensation capacitor of 47pF, the phase margin can increased further if this compensation capacitor is increased as well. A value of 47pF should suffice for the applications considered here.



<figure id="figure-3.9">
<img alt="laser_driver:gain_DAC" src="/images/gain_DAC.svg">
<figcaption>
Figure 3.9: Gain plot of the most and least significant bits (MSB and LSB resp.) of a <abbr title="Digital-to-Analogue Converter">DAC</abbr> circuit block to its output stage.
</figcaption>
</figure>



<figure id="figure-3.11">
<img alt="laser_driver:gain_adder" src="/images/gain_adder.svg">
<figcaption>
Figure 3.11: Gain plot of the sine and triangle inputs of the analogue adder circuit to its output stage for the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser.
</figcaption>
</figure>



The simulations indicate that the bandwidth of the <abbr title="Digital-to-Analogue Converter">DAC</abbr> circuits is 549kHz. The rise and fall times (10% to 90% of the final value) are 0.6586μs and 0.6584μs respectively. The bandwidth of the analogue adder circuit is 204kHz.

<h2 id="section-3.5">Voltage Controlled Current Source</h2>

Lasers are driven using a current signal. The voltage controlled current source will convert the output voltage signal of the <abbr title="Digital-to-Analogue Converter">DAC</abbr> to a current signal and drive the laser. The selected driver is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.16" >}}">3.16</a> and was selected for its linearity and simplicity. 

<h3>Circuit Analysis</h3>

The operating principle of the circuit in figure <a href="{{< ref "laser_driver.md#figure-3.16" >}}">3.16</a> is simple: the amplifier drives the base voltage of the pMOS transistor such that the same voltage appears at the minus clamp of the amplifier, and thus also at the lower clamp of $R\_\text{c}$. This generates a current $i$ that flows through the transistor and subsequently the laser (the base current of the transistor is negligible). The transistor should be rated such that it can provide the necessary (variable) voltage drop and current. The main disadvantage of this circuit is power dissipation by the resistor. This problem cannot be alleviated by means of a linear circuit. Non-linear circuits exist that offer better efficiency, but harmonics of the input signal can appear in this case. As a result, these are not a good option for our driver, where linearity is fundamental.

The input current of the laser is:

<div>
$$

i\_\text{laser} = \dfrac{V\_\text{CC} - v\_\text{in}}{R\_\text{c}}
\tag{3.7}

$$
</div>




<figure id="figure-3.16">
<img alt="laser_driver:voltage_to_current" src="/images/voltage_to_current.svg">
<figcaption>
Figure 3.16: Voltage controlled current source, linearly transforms voltage signal into current signal to drive laser.
</figcaption>
</figure>



The amount of power dissipated is (neglecting the power dissipated by the amplifier):
\begin{alignat}{3}
P_\text{total} &= P_\text{resistor} & + & P_\text{transistor} \\
&=\dfrac{(V_\text{CC} - v_\text{in} - v_\text{laser})^2}{R_\text{c}} &+& 
\br{V_\text{CC} - v_\text{in}} I_\text{D}
\end{alignat}

The resistors we are going to use, are rated with a maximum power dissipation of 0.25W. For the <abbr title="Distributed Feedback (Laser)">DFB</abbr> lasers, we will put several in parallel to lower the power dissipation per resistor. The values of $R\_\text{c}$ and the expected maximum power dissipation are listed in table <a href="{{< ref "laser_driver.md#table-3.1" >}}">3.1</a>. 

<table id="table-3.1">
<caption>Table 3.1: Resistor combinations for voltage-driven-current source and power dissipation.</caption>
<tr>
<th>laser</th>
<th>$R\_\text{c}$ [Ω]</th>
<th>$\max{P\_\text{resistor}}$ [mW]</th>
<th>$\max{P\_\text{transistor}}$  [mW]</th>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Vertical-Cavity Surface-Emitting Laser">VCSEL</abbr></td>
<td>56</td>
<td>6.19</td>
<td>31.4</td>
</tr>
<tr>
<td><abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>$51 \parallel 51$</td>
<td>309</td>
<td>109.5</td>
</tr>
<tr>
<td><abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr></td>
<td>$30 \parallel 30 \parallel 30$</td>
<td>546</td>
</table>


The <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser needs to be tied to $-V\_\text{CC}$ and not the ground reference, since the voltage drop over the transistor is otherwise too small.

<h3>Component Selection</h3>

The selected components are: PMF170XP for the transistor and LTC1152 for the amplifier. PMF170XP is a fast switching pMOS transistor that can dissipate up to 290mW, while rated at -20V for the drain-source voltage and -1A for the drain current [<a href="{{< relref "#citation10" >}}">10</a>]. We will need to place several of them in parallel for the <abbr title="Carbon Monoxide">CO</abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser. In principle, the LTC1152 is a rail-to-rail amplifier [<a href="{{< relref "#citation9" >}}">9</a>], but the design is made in such a way that the highest input voltage $v\_\text{in}$ is still 0.5V from the rail because distortion and other non-linear effects were present in simulations if the input signal came too close to the positive voltage rail. A similar argument (low voltage offset and drift are required) as in subsection <a href="{{< ref "laser_driver.md#subsection-3.1" >}}">3.1</a> can be made here. As a consequence, we utilise the same amplifier.

The circuit has sufficient stability when a bandwidth limiting capacitor is used of 47pF. A minimum phase margin of 68° is achieved while providing sufficient bandwidth for the signals of interest. A voltage-to-current gain plot is depicted in figure <a href="{{< ref "laser_driver.md#figure-3.10" >}}">3.10</a> for the <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser. As expected, the gain is $-28.13dBS = -20\log{R\_\text{c}}$. The bandwidth of this circuit is 533kHz.



<figure id="figure-3.10">
<img alt="laser_driver:gain_VI_converter" src="/images/gain_VI_converter.svg">
<figcaption>
Figure 3.10: Voltage to current gain plot for <abbr title="Carbon Dioxide">CO<sub>2</sub></abbr> <abbr title="Distributed Feedback (Laser)">DFB</abbr> laser circuit
</figcaption>
</figure>



<h2>Conclusion</h2>

In this chapter, we investigated laser characteristics and absorption features (section <a href="{{< ref "laser_driver.md#section-3.2" >}}">3.2</a>). After selecting the optimal features, we determined the required current signals (section <a href="{{< ref "laser_driver.md#section-3.3" >}}">3.3</a>). These currents will drive the laser input and produce the desired output wavelengths. These current signals originate from voltage signals. Different generation techniques are considered in section <a href="{{< ref "laser_driver.md#section-3.4" >}}">3.4</a> and the most suitable technique was selected for each of the three input signals: bias current, sine wave current and triangle current. Finally, in section <a href="{{< ref "laser_driver.md#section-3.1" >}}">3.1</a> and section <a href="{{< ref "laser_driver.md#section-3.5" >}}">3.5</a> circuits are designed and analysed that realise these signals and drive the laser. 

To conclude, we depict the overall transfer function from the digital input stage to the analogue current output that is fed into the laser in figure <a href="{{< ref "laser_driver.md#figure-3.8" >}}">3.8</a>. As expected, the gain is fairly low and consists of the following contributions for the most significant bit: (-6dB from digital input to output of the <abbr title="Digital-to-Analogue Converter">DAC</abbr> buffer amplifier, an additional -16.5dB at the adder output, and finally -28.1dBS for the voltage to current conversion, resulting in -50.6dBS). The bandwidth of this transfer function is 185kHz.



<figure id="figure-3.8">
<img alt="laser_driver:full_transfer_function" src="/images/full_transfer_function.svg">
<figcaption>
Figure 3.8: Gain from digital most and least significant bits of the triangle wave to the analogue output of the laser driver.
</figcaption>
</figure>



<ul>
<li id="citation1">[1]: "DFB Laser 2004nm", Datasheet, <em>Eblana Photonics</em>, Available: <a href='http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf'>http://www.frlaserco.com/uploads/files/20160707164325_EP2004-DM-B.pdf</a>.</li>
<li id="citation2">[2]: R. Wang, S. Sprengel, A. Malik, A. Vasiliev, G. Boehm, R. Baets, M. Amann and G. Roelkens, "Heterogeneously integrated III--V-on-silicon 2.3x µm distributed feedback lasers based on a type-II active region", <em>Applied Physics Letters</em>, 2016.</li>
<li id="citation3">[3]: "Online Database", http://hitran.iao.ru/home.</li>
<li id="citation4">[4]: T. J. Blasing, "Recent Greenhouse Gas Concentrations", 2016.</li>
<li id="citation5">[5]: J. Reid and D. Labrie, "Second-Harmonic Detection with Tunable Diode Lasers - Comparison of Experiment and Theory", <em>Applied Physics</em>, 1981.</li>
<li id="citation6">[6]: W. M. Leach, "Introduction to Electroacoustics and Audio Amplifier Design: The Class-D Amplifier", <em>Kendall/Hunt</em>, 2001.</li>
<li id="citation7">[7]: "Sine Wave Generation Techniques", <em>SNOA665C</em>, 2013.</li>
<li id="citation8">[8]: "E3640A -- E3649A Programmable DC Power Supplies", Datasheet, <em>Keysight Technologies</em>, Available: <a href='http://literature.cdn.keysight.com/litweb/pdf/5968-7355EN.pdf?id=1118372'>http://literature.cdn.keysight.com/litweb/pdf/5968-7355EN.pdf?id=1118372</a>.</li>
<li id="citation9">[9]: "LTC1152", Datasheet, <em>Linear Technology</em>.</li>
<li id="citation10">[10]: "PMF170XP", Datasheet, <em>NXP Semiconductors</em>, 2013.</li>
</ul>
