---
title: "Lab 7: Kalman Filter"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 6
layout: lab
---

In this lab, we implemented a Kalman Filter to combat the slow sampling rate of the sensors, as seen in Lab 6.

# Lab Tasks 

## Step Response

In order to use the Kalman Filter, we need to first figure out the A and B matrices used in the equation. To find these matrices, we need to get the step response. We would get these parameters from the step response:

<img src="https://render.githubusercontent.com/render/math?math=d = drag = \frac{u}{\dot{x}}">
<br>
<img src="https://render.githubusercontent.com/render/math?math=m = mass = \frac{-dt_{0.9}}{ln(1-0.9)}">

In order to calculate these values, I needed to drive the car at the top speed (not the actual top speed, but the top speed found form the previous lab; I used 80 PWM) and obtain 2 values: the steady state velocity, and the 90% rise time. The steady state velocity is the maximum velocity that the car reaches and is constant (so not accelerating), and the 90% rise time is the time it takes for the car to reach 90% of said constant velocity.

An issue I ran into was that I couldn't gather very accurate distance data when I put the car more than 4.5m away from the wall, but I would not reach steady state if I started at a nearer distance. To combat this, I took two data sets, one at starting at > 4.5m with distance and velocity data that was not accurate before reaching 4.5m, and one starting at 4m and not reaching stable state.

#### Starting Point: >4.5m, Reaching Stable State

<p align="left"><img src="../../images/lab7/d-stable.png" height="1000" width="1000"></p>

#### Starting Point: 4m, Not Reaching Stable State

<p align="left"><img src="../../images/lab7/d-unstable.png" height="1000" width="1000"></p>

The first set of graphs show that the steady state velocity is 2000mm/s, which the second set of graphs almost comes close to. Hence we know that <img src="https://render.githubusercontent.com/render/math?math={\dot{x}} = 2000\mms^{-1}">. Since we take <img src="https://render.githubusercontent.com/render/math?math=u"> to be 1, we have:
<img src="https://render.githubusercontent.com/render/math?math=d = drag = \frac{u}{\dot{x}} = \frac{1}{2000\mms^{-1}} = \frac{1}{2}m^{-1}s">

As for the 90% rise time, 90% of 2000mm/s is 1800mm/s so the 90% rise time is approximately 2.6s.

<p align="left"><img src="../../images/lab7/90rise.jpeg" height="500" width="500"></p>

Thus <img src="https://render.githubusercontent.com/render/math?math=m = mass = \frac{-dt_{0.9}}{ln(1-0.9)} = \frac{-0.5(2.6)}{ln(0.1)} = 0.5645">

We now have our A and B matrices as shown below:
<p align="left"><img src="../../images/lab7/matrices.jpeg" height="350" width="350"></p>

## Kalman Filter Setup

