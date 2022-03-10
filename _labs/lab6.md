---
title: "Lab 6: PID Speed Control"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 6
layout: lab
---

In this lab, we used what we learned from lecture about PID control and implemented it to our car. We first had to set up the bluetooth connection and make sure that the Artemis and laptop could communicate information smoothly so that the laptop can use the information from the sensors to make necessary adjustments, and send that information back to the Artemis and so on.

# Prelab

For this prelab, we needed to ensure the bluetooth connections works, and format the information as necessary. In order to do this, I edited the bluetooth Arduino script and started including the header files, initializing the global variables, and inclusing the necessary setup for the bluetooth, motors, ToF sensors and finally the IMU sensors. I did this in an incremental approach so only after the motors worked did I move on to the ToF sensor code, and finally I included the IMU sensor code.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/0e19027ef9ffa50bdc6c0e74cade2ebf.js"></script>

For the Jupyter notebook code, I followed the format of the example code given by the course staff and edited the handling of commands and made a few callback functions for the two ToF sensors and IMU data.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/16fb1dc93ca3e398f863e43fefa838ca.js"></script>

One other thing that I had not yet included in the data packets being sent was tiemstamps which I would add later on. These are useful to take into account for the amount of time taken to send packets of data.

# Lab Tasks

## Task A

I decided to start with Task A os as to get a feel of how to implement PID on simpler motion.