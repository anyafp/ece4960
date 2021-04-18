---
title: "Lab 3: Filters and FFT"
date: 2018-11-28T15:14:54+10:00
featured: true
weight: 5
layout: lab
---

## Objectives

* Understand low pass, high pass and bandpass filters using LTSpice
* Build a circuit with the microphone.
* Use MATLAB and Arduino to plot FFT graph of the signal picked up by the microphone.
* Amplify the sound outputted from the microphone.
* Implement low pass, high pass and bandpass filters to filter the amplified sound from the microphone.
* 

## Introduction

Lab 3 was split into three parts. In the first part, we used LTSpice to draw out various circuits that would be useful for the next few parts. This helped us visualize the circuits that would be built later on. It also would serve useful to plot the theoretical frequency response of the relevant setups. We then had to build a circuit that would use the microphone to collect sound and use it as input for the Arduino. Finally, we needed to make sure that the Arduino is able to read the output of the microphone. Since the analogRead() function is too slow, we were tasked to manually code the ADC. This would then be fed into a MATLAB script as input and the FFT would be calculated. With this information, the spectrum can be plotted.

In the second part of this lab, we needed to improve the microphone circuit. From the spectrum obtained in the previous part, the sound received was very miniscule (and this was with the volume output on our laptops being maxed out). As such, we needed to amplify the sound by tweaking the circuit. We then added different components to this circuit to make it a low pass and/or high pass filter using the circuit we had designed in part 1. The frequency response was plotted as well. Lastly, the bandpass filter was created using the Butterworth 4-pole filter discussed in class. The frequency response of this filter was also plotted

In the third part,

## LTSpice Basics

This section of the lab was used to get used to LTSpice and how it functions. After navigating through LTSpice, we needed to draw a low pass RC and high pass RC circuit (which is useful for the later parts of the lab). After drawing these circuits, the frequency response of these circuits was obtained. The circuits and graphs can be found below:

### Low Pass Circuit and Graph
![Low Pass Circuit](../../images/lab3/lowpass-circuit.png)
![Low Pass Frequency Response](../../images/lab3/lowpass-graph.png)
![Zoomed In Low Pass Frequency Response](../../images/lab3/lowpass-zoomin.png)

### High Pass Circuit and Graph
![High Pass Circuit](../../images/lab3/highpass-circuit.png)
![High Pass Frequency Response](../../images/lab3/highpass-graph.png)
![Zoomed In High Pass Frequency Response](../../images/lab3/highpass-zoomin.png)

The cut-off frequencies were obtained from the frequency response graph by finding the frequency value at -3dB. For the highpass filter, the cut-off frequency is ______________________.

## Building the Microphone Circuit

We then built the microphone circuit without amplification by refering to the layout given below:
<p align="center"><img src="../../images/lab3/noamp-circuit.png" height="130" width="500"></p>
<p align="center"><img src="../../images/lab3/noamp-image.jpeg" height="130" width="500"></p>