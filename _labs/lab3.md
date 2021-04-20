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

## Coding the Arduino and MATLAB

There must be some way for the Arduino to collect sound from the microphone circuit, and to use this data to analyze the frequency response.

### Arduino Code

Prior to this, we've been using the Arduino function analogRead() to read the analog value of a specific pin. However, this is too slow. We needed to code something faster so as to get real time accurate data with respect to the sound that the microphone picks up. We manually coded the ADC instead.

We used this code below that was found in the PDF provided called "Getting started with ADC."

```
#include <avr/io.h>
#include <stdbool.h>

uint16_t adcVal;
void ADC0_init(void);
uint16_t ADC0_read(void);
void ADC0_start(void);
bool ADC0_conersionDone(void);

void ADC0_init(void)
{
    /* Disable digital input buffer */
    PORTD.PIN6CTRL &= ~PORT_ISC_gm;
    PORTD.PIN6CTRL |= PORT_ISC_INPUT_DISABLE_gc;

    /* Disable pull-up resistor */
    PORTD.PIN6CTRL &= ~PORT_PULLUPEN_bm;
    ADC0.CTRLC = ADC_PRESC_DIV4_gc | ADC_REFSEL_INTREF_gc;
    ADC0.CTRLA = ADC_ENABLE_bm | ADC_RESSEL_10BIT_gc;

    /* Select ADC channel */
    ADC0.MUXPOS = ADC_MUXPOS_AIN6_gc;

    /* Enable FreeRun mode */
    /* CLK_PER divided by 4 */
    /* Internal reference */
    /* ADC Enable: enabled */
    /* 10-bit mode */
    ADC0.CTRLA |= ADC_FREERUN_bm;
}

uint16_t ADC0_read(void) {
    return ADC0.RES;
}

void ADC0_start(void) {
    ADC0.COMMAND = ADC_STCONV_bm;
}

bool ADC0_conersionDone(void) {
    return (ADC0.INTFLAGS & ADC_RESRDY_bm);
}

int main(void)
{
    ADC0_init();
    ADC0_start();

    while(1) {
        if (ADC0_conersionDone()) {
            adcVal = ADC0_read();
            /* In FreeRun mode, the next conversion starts automatically */
        }
    }
}
```

This code was tweaked to use the correct prescalar value and use the correct Arduino pin according to our own circuit design.

### MATLAB Code

Code was then written in MATLAB to fetch data from the Arduino. The file ```readData_INT_Canvas.m``` on Canvas handled most of the work related to generating the sound (chirp) for the microphone to pick up and reading and converting the values to be read from the serial port of the Arduino. What was left to do was perform Fourier analysis on the data so that we could analyze what was going on.