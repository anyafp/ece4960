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
<img src="https://render.githubusercontent.com/render/math?math=d = drag = \frac{u}{\dot{x}} = \frac{1}{2000\mms^{-1}} = 0.0005m^{-1}s">

As for the 90% rise time, 90% of 2000mm/s is 1800mm/s so the 90% rise time is approximately 2.6s.

<p align="left"><img src="../../images/lab7/90rise.jpeg" height="500" width="500"></p>

Thus <img src="https://render.githubusercontent.com/render/math?math=m = mass = \frac{-dt_{0.9}}{ln(1-0.9)} = \frac{-0.0005(2.6)}{ln(0.1)} = 5.64 \times 10^{-4}">

We now have our A and B matrices as shown below:
<p align="left"><img src="../../images/lab7/matrices.jpeg" height="350" width="350"></p>

## Kalman Filter Setup

In this section, we needed to specify our process noise and sensor noise covariance matrices. We need three covariance values: two for the process noise, and one for the sensor noise (or measurement noise) as seen below:

<p align="left"><img src="../../images/lab7/covar.jpeg" height="350" width="350"></p>

To start off the lab, I used the values below as ballpark values to adjust later. It is important to note that these are optimistic values as the covariances represent the trust in the model.

<img src="https://render.githubusercontent.com/render/math?math=\sigma_{1} = 10, \sigma_{2} = 10, \sigma_{3} = 20"> 

The C matrix used was the one provided in lecture:

<p align="left"><img src="../../images/lab7/cmat.jpeg" height="150" width="150"></p>


## Kalman Filter (Sanity Check)

Finally, we put together all the values we found in the previous section. Below is the code snippet showing a summary of the above two sections, where we obtained the d and m values, got the A, B and C matrices, and calculated the covariance matrices. We discretized the A and B matrices, and calculated the KF estimation before running it on some data I had from Lab 6.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/b46a0f1153e027b9fda3c32d68344e8a.js"></script>

Below is our input, which is the PWM values:

<p align="left"><img src="../../images/lab7/pwm.png" height="500" width="500"></p>

And the KF results along with the original data:

<p align="left"><img src="../../images/lab7/kf-test1.png" height="500" width="500"></p>

I wanted to test out how the different sigma values would affect the Kalman Filter, so I tried these values make the uncertainty of the model higher. This would mean we would put much less stress on our model and just follow the sensor values much closer.

<img src="https://render.githubusercontent.com/render/math?math=\sigma_{1} = 100, \sigma_{2} = 100, \sigma_{3} = 20">
<p align="left"><img src="../../images/lab7/kf-test2.png" height="500" width="500"></p>

Changing sig3 would put less stress on the sensors (in the case where we think that there is a lot of sensor noise, so it trusts the model more than the sensors).

<img src="https://render.githubusercontent.com/render/math?math=\sigma_{1} = 10, \sigma_{2} = 10, \sigma_{3} = 200">
<p align="left"><img src="../../images/lab7/kf-test3.png" height="500" width="500"></p>

## Kalman Filter on Robot

Now all we need to do is transfer the code from python to Arduino (which is easier than it sounds..). Below is the relevant code snippets of initializing all the variables needed, implementing the filter itself, and incorporating the filter into the PID control.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/c0a6a4d96aa05f449668c22ded394573.js"></script>

When testing this code for the first time, the speed of the car was slower than usual. Because of this, I changed the Kp value to 0.05 and kept the Kd value to 0.08. This resulted in the results below:

<p align="left"><img src="../../images/lab7/kf-car1.png" height="1500" width="1500"></p>

Although the car reached the goal very quickly, there were some jitters at the end where the car would still try to adjust its position. When checking the last distance recorded, I noticed that this was still quite far off of the goal of 300mm.

<script src="https://gist.github.com/anyafp/e98771ec2a6f7c9c14f8915c94bddf1a.js"></script>