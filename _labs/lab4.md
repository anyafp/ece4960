---
title: "Lab 4: Characterize Your Car"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 4
layout: lab
---

In this lab, we characterized our car to leverage the remote control and familiarize ourselves with the capabilities of the hardware. This is in hopes of replicating the manual behavior with autonomous control (and maybe exceed what manual control can do). We did some tests and took measurements in 2 different categories below.

# Prelab

# Category A Measurements

__What are the dimensions of the car?__

<p align="left"><img src="../../images/lab4/height-car.jpeg" height="600" width="600"></p>
<p align="left"><img src="../../images/lab4/length-car.jpeg" height="400" width="400"></p>

The dimensions of the car are very useful to have for 2 reasons. One is that it would be helpful to take into account when calculating the distance measured by the ToF sensor, depending on where it is placed. If it is at the front, it may be useful to know how far away the back of the car is in case we would want to do a flip. The other reason the dimensions would be helpful is so that we can consider the amount of space we have on the car for the board and sensors, as well as where we might want to place them.

__What is the weight?__

The car weighs 502g without the battery or the board/sensors. It is not completely clear to me at this point in time why this measurement might be useful but I can imagine it would be when taking friction into account. 

__How long does it take to charge?__

Since I took the batteries from the lab, I am not 100% sure exactly how long it takes to charge, but from estimating the charge at home, it takes about 1 hour and 45 minutes to charge. This is important to note as this is a long time and we should plan our car usage wisely.

__What is the battery life time?__

# Category B Measurements

__What is the range of speed?__
* This is battery dependent
* Consider using the ToF sensors to help you measure this

__What is the range of acceleration?__
* Consider using the ToF sensors and/or the IMU to help you measure this

__What is the braking distance?__
* Consider using the ToF sensors and/or the IMU to help you measure this

__What surfaces can it handle? Can you register the difference between surfaces using the sensors?__

__What stunts can it do?__
* Be sure to note the sequence of commands that corresponds with each stunt

__How reliably does the robot turn around its own axis?__

__How well can you operate the car with manual control?__
* e.g. Starting from ~5m distance, at full speed, how close can you get to a wall and stop the car, without a crash?
* e.g. How accurately can you hit four corners for a meter-scale square as fast as possible?
* e.g. Can you reliably reproduce trcks? (Describe which and what control inputs you give it)
* e.g. Can you make the robot balance on one set of wheels (front or back)?