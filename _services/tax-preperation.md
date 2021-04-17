---
title: "Lab 2: Light Following Robot Part 2"
date: 2018-11-18T12:33:46+10:00
featured: true
weight: 6
layout: service
---

## Objectives

* Understand the ADC.
* Understand how to use a H-Bridge to control the motors.
* Blink the LED without using the delay() function or interrupts.
* Successfully calibrate motors.
* Incorporate part 1 to use photoresistors as light sensors.

## Introduction

In Lab 2, we first began by understanding how the ADC (analog-to-digital converter) works. We then moved on to using the h-bridge to control motors, testing out the different speeds and directions that the wheels can move. Since the wheels were not completely identical, it was important to calibrate the wheels such that they would move at the same speed. Finally, integrating the photoresistors was an essential part of the lab so that the robot could sense the intensity of light and react to it appropriately.

## H-Bridge

<p align="center"><img src="../../images/lab2/hbridge.png" height="170" width="250"><br>H-Bridge Pinout (taken from lab handout)</p>

To connect and control the motors, we used the L293 H-Bridge, as shown above. Below are the explanations of the pins and their purposes.

### Power Supply
<p align="center"><img src="../../images/lab2/powersupply.png" height="130" width="500"></p>

### Output Terminals
<p align="center"><img src="../../images/lab2/output.png" height="95" width="500">
</p>

### Direction Control Pins
<p align="center"><img src="../../images/lab2/direction.png" height="160" width="500"></p>

### Speed Control Pins
<p align="center"><img src="../../images/lab2/speed.png" height="140" width="500">
</p>

After understanding what each pin of the h-bridge is for, the next step was to actually make the physical connections, as shown below.

<p align="center"><img src="../../images/lab2/connection.jpeg" height="240" width="300"><br>Connections between the Arduino, h-bridge, battery, and motors.</p>

## Wheel Actuation

In order to move the robot, I needed to understand what configuration would make the wheels move forwards, backwards, or in opposite directions (spin). The table below shows the control signals I used to implement this, and the video shows what the robot looks like when running the code with these configurations.

<p align="center"><img src="../../images/lab2/actuation.png" height="157" width="500"></p>

<p align="center"><img src="../../images/lab2/wheelgif.gif" height="240" width="450"></p>

## Wheel Calibration

It was very unlikely that the two wheels would spin at the same speed when given the same pulse width modulation (PWM) values. This was the same with the motors I had. As such, I did trial and error to find out which PWM values for each motor would result in the same spinning speed. This would be achieved when the robot moves forward in a straight line.

The video below shows the robot moving at a slow speed. In order to achieve this speed, I inputted a speed (the input of analogWrite along with the ENA/ENB) as 66 for motor A, and 60 for motor B.

<p align="center"><img src="../../images/lab2/slow.gif" height="240" width="450"></p>

The video below is the robot moving at medium speed, with speed inputs 78 for motor A and 70 for motor B.

<p align="center"><img src="../../images/lab2/med.gif" height="240" width="450"></p>

## Incorporating the Photosensor

The final step of this lab is to bring in what we did in lab 1 and use the photoresistor as a light sensor that would trigger the robot to perform different actions. Listed below are the requirements that the robot had to perform:

<p align="center"><img src="../../images/lab2/action.png" height="270" width="500"></p>

<p align="center"><iframe width="450" height="252" src="https://youtube.com/embed/q88cfr6-Gu4"></iframe></p>