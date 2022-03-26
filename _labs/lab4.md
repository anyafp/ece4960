---
title: "Lab 4: Characterize Your Car"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 6
layout: lab
---

In this lab, we characterized our car to leverage the remote control and familiarize ourselves with the capabilities of the hardware. This is in hopes of replicating the manual behavior with autonomous control (and maybe exceed what manual control can do). We did some tests and took measurements in 2 different categories below.

# Prelab

I swapped the wires for the battery so that it could be connected to the board with the colors matching (red positive, black negative). 

<p align="left"><img src="../../images/lab4/battery.png" height="600" width="600"></p>

# Category A Measurements

## What are the dimensions of the car?

<p align="left"><img src="../../images/lab4/height-car.jpeg" height="600" width="600"></p>
<p align="left"><img src="../../images/lab4/length-car.jpeg" height="400" width="400"></p>

The dimensions of the car are very useful to have for 2 reasons. One is that it would be helpful to take into account when calculating the distance measured by the ToF sensor, depending on where it is placed. If it is at the front, it may be useful to know how far away the back of the car is in case we would want to do a flip. The other reason the dimensions would be helpful is so that we can consider the amount of space we have on the car for the board and sensors, as well as where we might want to place them.

## What is the weight?

The car weighs 502g without the battery or the board/sensors. It is not completely clear to me at this point in time why this measurement might be useful but I can imagine it would be when taking friction into account. 

## How long does it take to charge? / What is the battery life time?

Since I took the batteries from the lab, I am not 100% sure exactly how long it takes to charge, but from estimating the charge at home, it takes about 1 hour and 45 minutes to charge. 

I played for the car for about 10 minutes before it died.

These values are important to note as this is a long time and we should plan our car usage wisely.

# Category B Measurements

## How reliably does the car turn around its own axis?

In order find out this measurement, we set up the experiment such that the car is placed on a marked point on the ground and is subsequently spun for 5 seconds. The distance between the marked spot and the final destination is recorded. Below is a video demonstrating a portion of this experiment:

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/iYTIlNhDkUA"></iframe></p>
<p></p>

<p align="left"><img src="../../images/lab4/axial-drift.png" height="250" width="250"></p>

It should be noted that we are unsure of the reliability of the ground and whether it is completely flat or not. We assumed so, though. The results were very consistent but the car had drifted a significant distance given it was only spinning for 5 seconds. This is an important value to note so that we can take into account and calibrate the robot such that it does the necessary adjustments to make sure it stays on the axis.

## How much does the car drift when going in a straight line?

The experiment was set up as follows:

<p align="left"><img src="../../images/lab4/drift-exp.jpeg" height="400" width="400"></p>

<p align="left"><img src="../../images/lab4/hor-drift.png" height="350" width="350"></p>

We believe this is a useful measurement as we can take into account the difference between the expected final destination and the actual final destination and calibrate the wheels such that it minimizes this value. As you can see in the table above, we took 6 measurements and varied the speed of the car as this affected the drift. It is expected that the faster the car goes, the more drift it would have. This is somewhat true, but not completely. Although a slower speed could potentially be more accurate as it takes small incremental steps instead of rushing to the end, this gives the car more time to veer off path. Hence, as seen in the results table, the medium speed it was going at was the most accurate (although we want to aim to achieve a fast, accurate robot!).

## What stunts can it do?

Below are videos of the stunts we tried, as well as their corresponding input controls. These stunts were interesting to try out and record their input controls so that we can experiment trying to replicate some of these stunts when automating the robot car.

### Car Stunt 1: Flips

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/2ilbThm87ng"></iframe></p>

Input Controls: Travel in one direction for 1-2s, then go in the other direction to flip the car.

### Car Stunt 2: Axis Rotation (but wheel is axis of rotation)

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/_nePzAeOl_s"></iframe></p>

Input Controls: Press the turn button and then immediately press the forward or backward button.

# Acknowledgments

I worked on this lab with Chidera Wokonko (you can find his website [here](https://pages.github.coecis.cornell.edu/ccw94/Fast-Robots/index.html){:target="_blank"}).