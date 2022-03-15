---
title: "Lab 6: PID Speed Control"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 6
layout: lab
---
In this lab, we used what we learned from lecture about PID control and implemented it to our car. We first had to set up the bluetooth connection and make sure that the Artemis and laptop could communicate information smoothly so that the laptop can use the information from the sensors to make necessary adjustments, and send that information back to the Artemis and so on.

# Prelab

# Lab Tasks

## Task A

For this task, we had to use PID control to ensure that the car stops 300m (30cm) away from the wall. In order to do this, I used only the P in PID. I calculated the error based on the ToF sensor reading and the target distance, obtained the speed from this value and a Kp value, and set the motor speed based on this as shown below:

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/c574ca392cbf173d52032e216ce01424.js"></script>

The limit of 5 and -5 for speeds on lines 4 and 6 are to give some leeway to allow for the robot to come to a stop because having the speed at 0 is too precise and specific (I assumed that the car would keep oscillating because it's difficult to get to exactly 0 error).

I added a limiting range to ensure that even as the speed goes beyond the maximum or below the minimum, it would still move.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/b6b1b1bc855544e59bfc137e753602c2.js"></script>

After ensuring that the P(ID) control works, I needed to figure out what Kp value works. I did a lot of trial and error to find the values and these are the plots generated with different starting points and Kp values

### Kp = 0.03; Starting Point = ~2m

Below is the video demonstration and data points with the above configurations. The time scale is a bit off since I had started the task a while after connecting to bluetooth, but that would just need a shift in the x-axis values which does not affect the y-axis readings.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/fh4uQRVdlgA"></iframe></p>
<p></p>

<p align="left"><img src="../../images/lab6/A-2m-03.png" height="1000" width="1000"></p>

As you can see from the video, with a Kp value of 0.03, the car slightly hits the wall before moving back slightly. This can also be seen in the data plots where the distance hits 0 before going back up to 300.

### Kp = 0.03; Starting Point = ~3m

And now the video demo and data points with starting point that is further away.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/ksUz9BnLbvE"></iframe></p>
<p></p>

<p align="left"><img src="../../images/lab6/A-3m-03.png" height="1000" width="1000"></p>

As expected, because the error starts off higher when the starting distance is further away, this gives the car less time to slow down and rams harder into the wall than when the starting distance was 2m instead.