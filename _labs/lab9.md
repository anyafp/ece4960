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

In a previous lab, I configured PID control for the front ToF sensor. In this lab, we need to use PID control for the IMU sensor readings instead. I decided to use the method that controlled angular speed, so that the car would turn at a relatively constant angluar speed. With the PID control, the baseline PWM value would be 90, and it would be adjusted based on the error from the angular velocity of 15 rad/s. If the car was rotating any slower than 15º/s, the error would be negative and the PWM value would increase above 90. If the car rotates faster than 15 rad/s, the error would be positive and this value (multiplied by the Kp value) would be subtracted from the PWM value of 90.

<script src="https://gist.github.com/anyafp/62acd55fed8fe9ea79d44f6bf7823c7b.js"></script>

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/iDF-Nt5ftWw"></iframe></p>
<p></p>

<p align="left"><img src="../../images/lab9/data.png" height="1500" width="1500"></p>

Looking at my sensor values and measurements for one of my runs, the PWM values show that the P(ID) controller works since I set the system such that it would aim to achieve a PWM value of 90 when there is 0 error. However, for the angular velocity, I set it to 15º/s, but the measured angular velocity does not oscillate around 15º/s until around the 17th second. I'm not entirely sure why this occurs because it would make sense for the angular velocity to be around 15 such that the error is 0, and that's why the PWM values could be maintained around 90. This could instead be the way I chose my Kp value and how it might have been too small and insignificant.

90 was the slowest PWM value I managed to use for the car to turn smoothly. With this, according to the graph, the angular velocity ranged from 10º/s - 50º/s with the average being around 30º/s - 35º/s. The total time it took to turn 360º was around 10s, so this means the average angular velocity was actually around 36º/s.

Looking at the video above, when spinning, the position of the car did not shift too much (however it would shift slightly every now and then, but reorientate itself back to the center of the tile). Since it did remain relatively in the center of the tile, we'd expect the readings to be the most accurate it can. However, we need to take into account that the pivot point is the center of the car and the sensor is placed at the front of the car. As such, for even more accurate results, we should subtract the length from the front of the sensor to the pivot point of the car, which might turn out to be more significant that we initially thought since the ToF sensor's precision is to the milimeter (but I did not do this in the rest of the lab).

## Read Out Distances

There were 5 coordinate points in the mapping that we had to map from. This is because the sensor data is more accurate when detecting objects nearby, and is noisy when detecting objects further away (especially if the rate of rotation is not slow enough) as evident in my polar plots. I did 2 runs per coordinate position to check for consistency of my data and plotted the polar plots below:

<p align="left"><img src="../../images/lab9/maps.png" height="1500" width="1500"></p>

My runs often did not align with each other, but once I rotated one of the run's data points (by adding or subtracting from the theta values), I got data points that matched up pretty well. However, they were not very accurate with respect to how I orientated my car initially so, to save myself some pain in the later part of this lab, I rotated both runs to match the ideal orientation by observing the car's environment and determining the corners that it should be detecting at certain angles.

## Merge and Plot Readings

Once I had all the data points for each coordinate, I needed to convert them into x and y coordinates, and combine all the data plots to plot one final mapping. Since the data points I had were in distance and theta, I had to use some basic math properties to convert them to x and y values.

<img src="https://render.githubusercontent.com/render/math?math=x = dist \times cos(\theta)">
<br>
<img src="https://render.githubusercontent.com/render/math?math=y = dist \times sin(\theta)">

After converting every single point to x and y coordinates, we can now use what we had learned in Lecture 2 on transformation matrices to convert the measurements from the distance sensor to the inertial reference frame of the room. As seen in the polar plots above, each run was taken at a certain coordinate in the map relative to the center (in feet). To calibrate the data points, we need to add the coordinate to the data points obtained (using the conversion 1 foot = 304.8 mm). 

Finally, I combined all these points into one array (ensuring that each x coordinate corresponded to its respective y coordinate) and plotted the x and y arrays.

I wrote a python script to do all of the above and below was the result with x and y being in mm.

<p align="left"><img src="../../images/lab9/map-raw3.png" height="500" width="500"></p>

## Convert to Line-Based Map

In order to make use of the simulator in the next lab, we need to manually estimate where the actual walls and obstacles are in the map. I observed the general location of the data points to get the coordinates of these lines and plotted them on top of my data plots. Below are the data points I used to plot the lines. They correspond to the start and end point of each line. There are four lists to account for the box in the middle.
>
    linex_wall = [-650, 2050, 2050, 250, 250, -250, -250, -1650, -1650, -670, -670]
    liney_wall = [1350, 1350, -1300, -1300, -900, -900, -1300, -1300, 75, 75, 1350]
>
    linex_box = [750, 1450, 1450, 750, 750]
    liney_box = [550, 550, -150, -150, 550]

<p align="left"><img src="../../images/lab9/map-est4.png" height="500" width="500"></p>

To check how accurate my data is, I used the data that Vivek had gotten (for Lab 10 in world.yaml) and plotte the actual measurements of the walls and boxes on top of my plot:
>
    real_linex_wall = [-1676, -1676, 1981, 1981, -762, -762, -1676]
    real_liney_wall = [152, -1371, -1371, 1371, 1371, 152, 152]
>
    real_linex_box1 = [762, 1371, 1371, 762, 762]
    real_liney_box1 = [-152, -152, 457, 457, -152]
>
    real_linex_box2 = [-152, -152, 152, 152, -152]
    real_liney_box2 = [-1371, -762, -762, -1371, -1371]

<p align="left"><img src="../../images/lab9/map-est-mes2.png" height="500" width="500"></p>