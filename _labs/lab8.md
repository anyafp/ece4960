---
title: "Lab 8: Stunts!"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 8
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, we utilized the past few labs to finally implement stunts with our robot car.

# Lab Tasks 

## Controlled Stunts

Since I chose Task A in Lab 6, for the controlled stunts, I was tasked to drive forward towards a wall, flip when it arrives ~500mm away from the wall, and drive away from the direction it came from. I made use of the Kalman Filter implemented in the previous lab to get the predicted distance and flip on time (since it took a significant amount of time to get sensor readings).

In order to do the flip, the car needed to travel towards the wall at a significant enough speed and, when it reached the desired distance from the wall, the PWM motors need to be driven in the opposite direction at maximum speed (PWM value of 255) in order to achieve a flip. This was not possible using the PID control since the car would slow down as it reached the desired position to flip. With this in mind, I used the following Arduino code to implement this.

<script src="https://gist.github.com/anyafp/2fd7e5a020892c3062f74be12d600947.js"></script>

The stunt was very dependent on many factors to make it work. Some of these factors are listed below:

- Battery charge
- How dusty the wheels and mat were
- The PWM values of the left vs right motor

Some of these factors would affect my car in unexpected ways (e.g. changing the battery to a new one would make my car go more to the left after the flip) which was combatted by just doing the run many, many times.

After many many tries, I got one run where the car flips and goes back to relatively near the start line (whereas many other runs would pivot and go straight to the side). The output data and video demonstration can be seen below.

<p align="left"><img src="../../images/lab8/taskAstunt.png" height="1500" width="1500"></p>

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/6ZMhJzGyAGM"></iframe></p>
<p></p>

The KF value no longer changes after reaching a certain distance since I no longer use any sensor/KF values during/after the flip. The sensor values are still updated, even though not used. The PWM values are predictable and expected since I set it to 250 before reaching 500mm, -255 for 1 second during the flip, -40 for 0.2 seconds to slow down (which is not captured in the graph), and finally comes to a stop.

## Open Loop, Repeatable Stunts

For this section, I no longer needed to use sensor data to make a stunt. Instead, I could do an open loop stunt where I would hard code the control sequence in the Arduino IDE.

### Stunt 1: Spin In Place

I decided to start simple and try the stunt where I just spin in place.

<script src="https://gist.github.com/anyafp/c743b89cae931c6e9bfc22dbab584080.js"></script>

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/FcGBCpbYj9w"></iframe></p>
<p></p>

### Stunt 2: Ramp Up

Since spinning in place was relatively simple and was implemented successfully, I wanted to try driving the car up a ramp and making it flip. Below are the three tries of this stunt. They're not completely identical since it required precise placement of the car in the same location for the stunt to be exactly the same. 

<script src="https://gist.github.com/anyafp/5d32c9d8e2475919f33d9578c13a920a.js"></script>

The code for this stunt is relatively simple since we just want to drive the car forward and jerk it backwards to create the flip.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/1OnmzVhPcCU"></iframe></p>
<p></p>

## Bloopers

Not sure how my ToF sensor still works..

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/cEcRWQi84ms"></iframe></p>
<p></p>