---
title: "Lab 9: Mapping"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 9
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, we attempt to create a mapping of a room in the lab, and use this map in later localization and navigation tasks. In order to build the map, the robot was placed at several positions in the classroom and spun around its axis to caputre the ToF sensor readings.

# Lab Tasks

## PID Control

In a previous lab, I configured PID control for the front ToF sensor. In this lab, we need to use PID control for the IMU sensor readings instead. I decided to use the method that controlled angular speed, so that the car would turn at a relatively constant angluar speed. With the PID control, the baseline PWM value would be 90, and it would be adjusted based on the error from the angular velocity of 15 rad/s. If the car was rotating any slower than 15 rad/s, the error would be negative and the PWM value would increase above 90. If the car rotates faster than 15 rad/s, the error would be positive and this value (multiplied by the Kp value) would be subtracted from the PWM value of 90.

<script src="https://gist.github.com/anyafp/62acd55fed8fe9ea79d44f6bf7823c7b.js"></script>

There were 5 coordinate points in the mapping that we had to map from. This is because the sensor data is more accurate when detecting objects nearby, and is noisy when detecting objects further away (especially if the rate of rotation is not slow enough) as evident in my polar plots. I did 2 runs per coordinate position to check for consistency of my data and plotted the polar plots below:

<p align="left"><img src="../../images/lab9/maps.png" height="1500" width="1500"></p>

My runs often did not align with each other, but once I rotated one of the run's data points (by adding or subtracting from the theta values), I got data points that matched up pretty well. However, they were not very accurate with respect to how I orientated my car initially so, to save myself some pain in the later part of this lab, I rotated both runs to match the ideal orientation by observing the car's environment and determining the corners that it should be detecting at certain angles.

Once I had all the data points for each coordinate, I needed to convert them into x and y coordinates, and combine all the data plots to plot one final mapping. Since the data points I had were in distance and theta, I had to use some basic math properties to convert them to x and y values.

<img src="https://render.githubusercontent.com/render/math?math=x = dist \times cos(\theta)">
<br>
<img src="https://render.githubusercontent.com/render/math?math=y = dist \times sin(\theta)">

After converting every single point to x and y coordinates, we can now use what we had learned in Lecture 2 on transformation matrices to convert the measurements from the distance sensor to the inertial reference frame of the room. As seen in the polar plots above, each run was taken at a certain coordinate in the map relative to the center (in feet). To calibrate the data points, we need to add the coordinate to the data points obtained (using the conversion 1 foot = 304.8 mm). 

Finally, I combined all these points into one array (ensuring that each x coordinate corresponded to its respective y coordinate) and plotted the x and y arrays.

I wrote a python script to do all of the above and below was the result:

<p align="left"><img src="../../images/lab9/map-2.png" height="1500" width="1500"></p>

The x and y values are in mm and I drew over the data points to somewhat extrapolate the data points in the plot on the right.